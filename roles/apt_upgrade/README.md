# Role -  apt_upgrade

Upgrade Debian based systems using APT or Aptitude.

## 📋 Requirements

* [ansible.builtin.apt](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html)
* [ansible.builtin.stat](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/stat_module.html)
* [ansible.builtin.reboot](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/reboot_module.html)

## 🧩 Variables

| name                                | type   | required | choices                                                                                                                          | default       | description                              |
| ----------------------------------- | ------ | -------- | -------------------------------------------------------------------------------------------------------------------------------- | ------------- | ---------------------------------------- |
| `apt_upgrade__cleanup.autoclean`    | bool   | ❌       |                                                                                                                                  | `true`        | APT `autoclean` after upgrade            |
| `apt_upgrade__cleanup.autoremove`   | bool   | ❌       |                                                                                                                                  | `false`       | APT `autoremove` after upgrade           |
| `apt_upgrade__update.type`          | string | ❌       | see [ansible.builtin.apt](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html#parameter-upgrade) | `full`        | APT upgrade type                         |
| `apt_upgrade__update.refresh_cache` | bool   | ❌       |                                                                                                                                  | `true`        | run APT update before upgrading packages |
| `apt_upgrade__update.force_apt_get` | bool   | ❌       |                                                                                                                                  | `false`       | use `apt-get` instead of `aptitude`      |
| `apt_upgrade__reboot.always`        | string | ❌       | `if_required`<br>`always`                                                                                                        | `if_required` | when to reboot                           |

## 💻 Example Usage

```yaml
---
# group_vars
apt_upgrade__cleanup:
  autoclean: true
  autoremove: true

apt_upgrade__update:
  type: dist
  refresh_cache: true
  force_apt_get: true

---
# Playbook
- name: Proxmox Virtual Environment
  hosts: linux
  tasks:

    - name: Upgrade packages
      ansible.builtin.import_role:
        name: apt_upgrade
      when: ansible_os_family == 'Debian'
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
