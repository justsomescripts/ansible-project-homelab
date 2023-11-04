# Role -  unattended_upgrades_configuration

Install and configure `unattended-upgrades` on Debian or Debian-based systems.

## 📋 Requirements

* [ansible.builtin.template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
* [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
* [ansible.builtin.package](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/package_module.html)

## 🧩 Variables

| name                                                               | type           | required | choices                                                    | default                                                                                                                                                                                                                                                              | description                                                                    |
| ------------------------------------------------------------------ | -------------- | -------- | ---------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| `unattended_upgrades_configuration__autoupgrade.cache`             | bool           | ❌       |                                                            | `true`                                                                                                                                                                                                                                                               | automatically update APT package cache                                         |
| `unattended_upgrades_configuration__autoupgrade.packages`          | bool           | ❌       |                                                            | `false`                                                                                                                                                                                                                                                              | automatically update packages (according to config)                            |
| `unattended_upgrades_configuration__autoupgrade.days`              | array          | ❌       | localized abbreviated<br>full name<br>integer (0 = Sunday) | `- Mon`<br>`- Tue`<br>`- Wed`<br>`- Thu`<br>`- Fri`<br>`- Sat`<br>`- Sun`                                                                                                                                                                                            | days to run auto update                                                        |
| `unattended_upgrades_configuration__packages.origins`              | array of dicts | ❌       | list of origins + codenames + labels                       | `- origin: Debian`<br>`  codename: ${distro_codename}`<br>`  label: Debian`<br>`- origin: Debian`<br>`  codename: ${distro_codename}`<br>`  label: Debian-Security`<br>`- origin: Debian`<br>`  codename: ${distro_codename}-security`<br>`  label: Debian-Security` | package origins for unattended upgrades                                        |
| `unattended_upgrades_configuration__packages.blacklist`            | array          | ❌       | package names in repo                                      |                                                                                                                                                                                                                                                                      | packages excluded from automatic upgrades                                      |
| `unattended_upgrades_configuration__dpkg.autofix_interrupded`      | bool           | ❌       |                                                            | `true`                                                                                                                                                                                                                                                               | fix previousely interrupded upgrades                                           |
| `unattended_upgrades_configuration__dpkg.minimal_steps`            | bool           | ❌       |                                                            | `true`                                                                                                                                                                                                                                                               | minimum number of actions during every step (allows e.g. reboot during update) |
| `unattended_upgrades_configuration__dpkg.install_on_shutdown`      | bool           | ❌       |                                                            | `false`                                                                                                                                                                                                                                                              | install before reboot instead of immediately                                   |
| `unattended_upgrades_configuration__dpkg.autoremove.kernel`        | bool           | ❌       |                                                            | `true`                                                                                                                                                                                                                                                               | autoremove unused kernel images                                                |
| `unattended_upgrades_configuration__dpkg.autoremove.unused`        | bool           | ❌       |                                                            | `false`                                                                                                                                                                                                                                                              | autoremove unused packages                                                     |
| `unattended_upgrades_configuration__dpkg.autoremove.new_unused`    | bool           | ❌       |                                                            | `true`                                                                                                                                                                                                                                                               | autoremove newly orphaned packages                                             |
| `unattended_upgrades_configuration__dpkg.allow_downgrade`          | bool           | ❌       |                                                            | `true`                                                                                                                                                                                                                                                               | allow package downgrades                                                       |
| `unattended_upgrades_configuration__report.mail.user`              | string         | ❌       | Linux user                                                 | `root`                                                                                                                                                                                                                                                               | mail alias user (usually root)                                                 |
| `unattended_upgrades_configuration__report.mail.sender`            | string         | ❌       | mail address                                               | `mail@example.com`                                                                                                                                                                                                                                                   | sender mail address, e.g. configured in Postfix                                |
| `unattended_upgrades_configuration__report.level`                  | string         | ❌       | `always`<br>`only-on-error`<br>`on-change`                 | `on-change`                                                                                                                                                                                                                                                          | when to send notification mails                                                |
| `unattended_upgrades_configuration__report.verbose`                | bool           | ❌       |                                                            | `false`                                                                                                                                                                                                                                                              | verbose notifications                                                          |
| `unattended_upgrades_configuration__report.debug`                  | bool           | ❌       |                                                            | `false`                                                                                                                                                                                                                                                              | debug mode                                                                     |
| `unattended_upgrades_configuration__system.restart_daemons`        | bool           | ❌       |                                                            | `true`                                                                                                                                                                                                                                                               | add package `needrestart` to restart daemons after upgrades                    |
| `unattended_upgrades_configuration__system.auto_reboot.enabled`    | bool           | ❌       |                                                            | `false`                                                                                                                                                                                                                                                              | reboot after automatic update                                                  |
| `unattended_upgrades_configuration__system.auto_reboot.with_users` | bool           | ❌       |                                                            | `false`                                                                                                                                                                                                                                                              | reboot after automatic update with users logged in                             |
| `unattended_upgrades_configuration__system.auto_reboot.time`       | bool           | ❌       | `hh:mm`                                                    | `05:00`                                                                                                                                                                                                                                                              | time of automatic reboot                                                       |
| `unattended_upgrades_configuration__system.syslog.enabled`         | bool           | ❌       |                                                            | `false`                                                                                                                                                                                                                                                              | log to syslog                                                                  |
| `unattended_upgrades_configuration__system.syslog.facility`        | string         | ❌       | syslog facility                                            | `daemon`                                                                                                                                                                                                                                                             | linux syslog facility                                                          |

## 💻 Example Usage

```yaml
---
# group_vars
unattended_upgrades_configuration__packages:
  origins:
    - origin: Debian
      codename: ${distro_codename}
      label: Debian
    - origin: Debian
      codename: ${distro_codename}
      label: Debian-Security
    - origin: Debian
      codename: ${distro_codename}-security
      label: Debian-Security
    - origin: Debian
      codename: ${distro_codename}-updates
      label: false
    - origin: Proxmox
      codename: ${distro_codename}
      label: Proxmox Ceph Enterprise Debian Repository
    - origin: Proxmox
      codename: ${distro_codename}
      label: Proxmox VE Enterprise Debian Repository
  blacklist: []

---
# Playbook
- name: Proxmox Virtual Environment
  hosts: proxmox
  remote_user: "{{ remote_user_configuration__admin.user }}"
  tasks:

    - name: Configure Linux system
      tags:
        - linux_system
      block:

...

        - name: Configure automatc updates
          ansible.builtin.import_role:
            name: unattended_upgrades_configuration
          tags:
            - update_configuration
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
