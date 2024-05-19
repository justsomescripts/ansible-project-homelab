# Role -  chrony

Configure networking using [Systemd Networkd](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html).

## 📋 Requirements

* [ansible.builtin.template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
* [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)

## 🧩 Variables

| name                                    | type   | required | choices                                                                                                                         | default           | description                                    |
| --------------------------------------- | ------ | -------- | ------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ---------------------------------------------- |
| `network__filename`                     | string | ✅       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BMatch%5D%20Section%20Options)   |                   | destination file unter `/etc/systemd/network/` |
| `network__match.type`                   | string | ✅       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BMatch%5D%20Section%20Options)   | `Name`            | interface to match                             |
| `network__match.value`                  | string | ✅       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BMatch%5D%20Section%20Options)   | `eth0`            | interface to match                             |
| `network__match.virtualization.type`    | string | ❌       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BMatch%5D%20Section%20Options)   | `container`       | virtualization options                         |
| `network__match.virtualization.enabled` | bool   | ❌       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BMatch%5D%20Section%20Options)   | `false`           | virtualization enabled                         |
| `network__network.dhcp`                 | string | ❌       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BNetwork%5D%20Section%20Options) | `ipv6`            | enable DHCP                                    |
| `network__network.address`              | string | ✅       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BNetwork%5D%20Section%20Options) |                   | ip address                                     |
| `network__network.gateway`              | string | ✅       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BNetwork%5D%20Section%20Options) |                   | default gateway                                |
| `network__network.link_local`           | string | ❌       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BNetwork%5D%20Section%20Options) | `ipv6`            | enable link-local addressing                   |
| `network__network.lldp`                 | string | ❌       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BNetwork%5D%20Section%20Options) | `'yes'`           | enable LLDP                                    |
| `network__network.emit_lldp`            | string | ❌       | [documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#%5BNetwork%5D%20Section%20Options) | `customer-bridge` | emit LLDP                                      |

## 💻 Example Usage

```yaml
---
# host_vars
network__match:
  type: Name
  value: host0
  virtualization:
    type: container
    enabled: true

network__network:
  dhcp: ipv6
  link_local: ipv6
  lldp: 'yes'
  emit_lldp: customer-bridge

---
# Playbook
- name: Container Host Jail
  hosts: ctr.host.dgries.internal
  remote_user: "{{ remote_user__admin.user }}"
  tasks:

    - name: Configure Linux system
      tags:
        - linux_system
      block:

        - name: Configure network
          ansible.builtin.import_role:
            name: network
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
