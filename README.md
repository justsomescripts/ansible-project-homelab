# Simple Ansible Homelab

This repo has one readable playbook that applies a common Debian baseline:

- admin user
- SSH key login
- custom SSH port
- common packages
- Chrony
- unattended upgrades and automatic service restarts with needrestart
- email alerts through the iCloud Mail SMTP relay
- ZED automatically when ZFS is installed

It also applies the small Proxmox repository/admin setup. Hosts in the
`physical_hosts` inventory group get UPS/NUT configuration. The only Debian VM is
`ctr.host.dgries.internal`.

The repo is intentionally small now. The maintained path is [`setup.yml`](setup.yml)
plus `inventory.yaml`.

## Requirements

Install Ansible, `sshpass`, the Bitwarden CLI, and the `community.general`
collection on your Mac.

```bash
brew install ansible sshpass bitwarden-cli
ansible-galaxy collection install community.general
```

`sshpass` is only needed for the first run if the server still needs password SSH.
The Bitwarden CLI must use the self-hosted Vaultwarden server. Configure it while
logged out, then log in and export its session in the shell that runs Ansible:

```bash
bw config server https://vault.dgries.de
bw login
export BW_SESSION="$(bw unlock --raw)"
bw status
```

If `bw` is already logged in to a different server, run `bw logout` before
`bw config server`. The final status must show `https://vault.dgries.de` and
`"status": "unlocked"`.

## Configure Hosts

Copy the example inventory and edit the hosts, user, port, and Bitwarden item
names.

```bash
cp inventory.example.yaml inventory.yaml
vim inventory.yaml
```

The important part is this:

```yaml
all:
  vars:
    ansible_user: admin
    ansible_port: 1337
    ansible_python_interpreter: /usr/bin/python3
    ansible_ssh_common_args: -o ForwardAgent=yes
    admin_user: admin
    ssh_port: 1337
    ssh_hardening_enabled: true
    ssh_agent_auth_enabled: true
    bitwarden_ssh_public_key_item: Proxmox VE
    bitwarden_ssh_public_key_field: SSH Public Key
    bitwarden_admin_password_item: Proxmox VE
    bitwarden_admin_password_field: password
    bitwarden_ups_password_item: Proxmox VE
    bitwarden_ups_password_field: UPS Password
    bitwarden_mail_item: Infrastructure Mail
    bitwarden_mail_username_field: username
    bitwarden_mail_password_field: password
    alert_email: infra@dgries.de
    mail_sender: infra@dgries.de
    mail_relay_host: smtp.mail.me.com
    mail_relay_port: 587
    unattended_upgrades_enabled: true
    proxmox_admin_realm: pam
```

`ansible_port` is the port Ansible connects to. `ssh_port` is the port Ansible
configures on the server. After setup, those should normally be the same.

The Linux `admin` account is used for key-only SSH, agent-authenticated sudo, and
the Proxmox web interface through Linux PAM. Its password comes from the
`password` field of the `Proxmox VE` Bitwarden item. SSH password login remains
disabled. Sudo uses `libpam-ssh-agent-auth` and the same public key retrieved from
Bitwarden; the private key must be loaded in the SSH agent on your Mac.

On Proxmox, the playbook disables the subscription-only enterprise repository
and enables the public `pve-no-subscription` repository in
`/etc/apt/sources.list.d/proxmox.sources`. Debian's installer-created Trixie,
updates, and security repositories are left in place. The package installation
task refreshes all enabled repositories before installing updates.

Chrony uses Debian's packaged default NTP pools. The playbook removes the old
custom `homelab.sources` override if it exists.

Postfix relays local system mail through iCloud Mail. The `Infrastructure Mail`
Bitwarden item must contain the full iCloud Mail address used to authenticate to
Apple's SMTP server and an Apple app-specific password. This may be the account's
`@icloud.com` address even though the configured sender and recipient are
`infra@dgries.de`. ZED alerts, unattended-upgrade reports, UPS state changes, and
mail addressed to the local `root` account go to `infra@dgries.de`.

The inventory host names are used as-is. Do not add `ansible_host` unless DNS does
not resolve and you really need to point a host name at a different address.

ZED is installed and configured on any host where the `zfsutils-linux` package is
already installed. You can tune it per host:

```yaml
physical_hosts:
  children:
    proxmox:
      hosts:
        pve.host.dgries.internal:
          zed_notify_verbose: true
          zed_notify_interval: 3600
          ups_mode: netclient
          ups_name: Eaton3S
          ups_remote_host: tns.host.dgries.internal
          ups_remote_port: 3493
```

UPS/NUT is enabled automatically for `physical_hosts`. Set `ups_enabled: false`
on an individual physical host if it does not have a UPS attached.

The example configures Proxmox as a NUT network client. On TrueNAS SCALE, keep
the working UPS in Master mode, set its identifier to `Eaton3S`, use the same
`upsmon` password stored in Bitwarden, and enable Remote Monitor. TrueNAS then
serves UPS status on TCP port 3493. You can verify it from Proxmox with:

```bash
upsc -l tns.host.dgries.internal:3493
upsc Eaton3S@tns.host.dgries.internal:3493
```

The first command prints the exact UPS identifier exported by TrueNAS. Its output
must match `ups_name` in the inventory.

For a UPS connected directly to a Debian host instead, use `ups_mode: standalone`
with `ups_driver` and `ups_port`.

## First Run

During the Proxmox installer, configure:

- hostname `pve.host.dgries.internal`
- its final static IP, gateway, and DNS
- a temporary root password for the first SSH connection

The playbook does not replace the hostname or network configuration. Before
running it, make sure both `pve.host.dgries.internal` and
`tns.host.dgries.internal` resolve from Proxmox, then enable Remote Monitor for
the `Eaton3S` UPS in TrueNAS.

Create `inventory.yaml` from the example and confirm these Bitwarden fields:

- `Proxmox VE` / `SSH Public Key`: public key for the Linux `admin` account
- `Proxmox VE` / `password`: PAM password for the Linux and Proxmox `admin` account
- `CTR` / `password`: separate Linux `admin` password for `ctr.host.dgries.internal`
- `Proxmox VE` / `UPS Password`: TrueNAS NUT monitor password
- `Infrastructure Mail` / `username`: full iCloud Mail login address
- `Infrastructure Mail` / `password`: Apple app-specific password

Unlock the Bitwarden CLI, verify the initial root connection, and run the
bootstrap pass. This leaves root enabled and keeps SSH on port 22:

```bash
ssh-add -l
ssh root@pve.host.dgries.internal
ansible-playbook -i inventory.yaml setup.yml \
  --limit pve.host.dgries.internal \
  -k \
  -e ansible_user=root \
  -e ansible_port=22 \
  -e ansible_become=false \
  -e ssh_hardening_enabled=false
```

Verify the replacement account before disabling root:

```bash
ssh -A -p 22 admin@pve.host.dgries.internal
sudo -n true
```

Also open `https://pve.host.dgries.internal:8006` and log in as `admin` using
the **Linux PAM standard authentication** realm.

Only after both checks succeed, run the hardening pass while SSH still listens
on port 22:

```bash
ansible-playbook -i inventory.yaml setup.yml \
  --limit pve.host.dgries.internal \
  -e ansible_port=22
```

That second pass locks the root password, sets `PermitRootLogin no`, disables SSH
password authentication, limits SSH to `admin`, and switches SSH to port 1337.
The web login identity is `admin@pam`.

## Normal Runs

After the first run, use:

```bash
ansible-playbook -i inventory.yaml setup.yml
```

If an existing `admin` account can use sudo but still requires its password,
bootstrap that host once with `-K`. After this run, sudo authenticates using the
forwarded SSH agent:

```bash
ansible-playbook -i inventory.yaml setup.yml \
  --limit ctr.host.dgries.internal \
  -K
```

If a host fails to connect after changing the SSH port, test it directly:

```bash
ssh -A -p 1337 admin@pve.host.dgries.internal
```

## Per-Host Overrides

You can override variables per host in `inventory.yaml`:

```yaml
debian_vms:
  hosts:
    ctr.host.dgries.internal:
      ssh_port: 2222
      ansible_port: 2222
```

For the first run on that same host, still override the connection port:

```bash
ansible-playbook -i inventory.yaml setup.yml --limit ctr.host.dgries.internal -k -e ansible_user=root -e ansible_port=22 -e ansible_become=false -e ssh_hardening_enabled=false
```
