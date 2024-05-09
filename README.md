<a name="readme-top"></a>
<!-- omit in toc -->
# Ansible Homelab

This repository houses Ansible resources for configuring my personal homelab.
It covers the setup of physical hosts, such as configuring UPS systems and ZFS Event
Daemon with Postfix as an SMTP relay for critical issue notifications, as well as
declarations for virtual machines.

<!-- omit in toc -->
## 📚 Table of Contents

- [ℹ️ About The Project](#ℹ️-about-the-project)
- [✈️ Getting Started](#️-getting-started)
- [📖 Usage](#-usage)
- [🛣 Roadmap](#-roadmap)
  - [Not planned due to introduction of systemd-nspawn on TrueNAS Scale](#not-planned-due-to-introduction-of-systemd-nspawn-on-truenas-scale)
- [🤝 Contributing](#-contributing)
- [📜 License](#-license)
- [📬 Contact](#-contact)


<p align="right">(<a href="#readme-top">back to top</a>)</p>

## ℹ️ About The Project

This project aims to be completely declarative and able to bootstrap an empty environment
in to use for disaster recovery and initial setup. The roles are written to be re-usable
and independent of the underlying Linux OS family. All roles are used / tested on Debian-based
systems.

Built with [Ansible](https://www.ansible.com/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## ✈️ Getting Started

The following is necessary to start using this repository:

**Required packages**:

- `ansible`
- `sshpass` (for initial setup)
- `python-passlib`
- `jq`
- `python-jmespath`

**Optional packages**:

- `bitwarden-cli` (for getting secrets)
- `sshpass` (for populating public SSH keys during initial setup)

**Ansible collections**:

- `ansible.builtin`
- `community.general` (for bitwarden lookups)
- `community.proxmox`

**Installation example on Arch (btw)**

The `ansible` package includes all required collections

```bash
sudo pacman --sync ansible sshpass python-passlib jq python-jmespath
```

**Role documentation**:

- [apt_upgrade](roles/apt_upgrade/README.md)
- [chrony](roles/chrony/README.md)
- [jailmaker](roles/jailmaker/README.md)
- [postfix](roles/postfix/README.md)
- [pve_user](roles/pve_user/README.md)
- [remote_user](roles/remote_user/README.md)
- [unattended_upgrades](roles/unattended_upgrades/README.md)
- [ups](roles/ups/README.md)
- [zed](roles/zed/README.md)

## 📖 Usage

The Playbooks should be called using the `site.yaml` Playbook. It includes tags
for various combination of steps:

```bash
ansible-playbook site.yaml --tags init                 # bootstrap a new environment
ansible-playbook site.yaml --tags init --limit proxmox # bootstrap all proxmox nodes environment
ansible-playbook site.yaml --tags upgrade              # upgrade all systems
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ROADMAP -->
## 🛣 Roadmap

- [x] Systemd-nspawn containers on TrueNAS Scale (Jailmaker)
- [ ] Centralized monitoring/logging
- [ ] Migrate Nextcloud to systemd-nspawn (currently Scale app)
- [ ] Migrate gitea to systemd-nspawn (currently Scale app)
- [ ] Migrate Paperless-NGX to systemd-nspawn (currently Scale app)
- [ ] Migrate external proxy (FRP) to simple tunnel (Wireguard)

### Not planned due to introduction of systemd-nspawn on TrueNAS Scale
- [ ] ~~Proxmox~~
    - [ ] ~~[Ubuntu Cloud image](https://cloud-images.ubuntu.com/) VM template~~
    - [ ] ~~VM storage backend (probably [Ceph](https://pve.proxmox.com/wiki/Deploy_Hyper-Converged_Ceph_Cluster))~~
    - [ ] ~~[Proxmox Backup Server](https://www.proxmox.com/de/proxmox-backup-server/uebersicht)~~
- [ ] ~~Kubernetes~~
    - [ ] ~~Setup [RKE2](https://docs.rke2.io/)~~
    - [ ] ~~Setup [Longhorn](https://longhorn.io/) storage~~

See the [open issues](https://github.com/justsomescripts/ansible-homelab/issues) for a full list of proposed features (and known issues).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🤝 Contributing

Although this is project is specifically targeted to my personal setup and probably
not re-used, contributions are *greatly appreciated*. So if you have any suggestions
for improvements or fixes, feel free to open an [issue](https://github.com/justsomescripts/ansible-homelab/issues) or a [pull request](https://github.com/justsomescripts/ansible-homelab/pulls)

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 📜 License

Distributed under the *GNU GENERAL PUBLIC LICENSE*. See [`LICENSE`](LICENSE) for more information.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 📬 Contact

David Gries - [@dgries](https://www.linkedin.com/in/dgries/) - mail@dgries.de

<p align="right">(<a href="#readme-top">back to top</a>)</p>
