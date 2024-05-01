# Role - zed

Install and configure [ZFS Event Daemon](https://packages.debian.org/bookworm/zfs-zed).

## 📋 Requirements

* [ansible.builtin.template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
* [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
* [ansible.builtin.package](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/package_module.html)

## 🧩 Variables

| name                                                 | type   | required | choices                | default                    | description                                                            |
| ---------------------------------------------------- | ------ | -------- | ---------------------- | -------------------------- | ---------------------------------------------------------------------- |
| `zed__mail.recipient`                  | string | ❌       | linux user             | `root`                     | username to use for mail alias                                         |
| `zed__mail.program`                    | string | ❌       | sendmail program flags | `mail`                     | additional flags for sendint mail                                      |
| `zed__mail.options`                    | string | ❌       | sendmail program       | `-s '@SUBJECT@' @ADDRESS@` | path / name of mail program to use                                     |
| `zed__notify.verbose`                  | bool   | ❌       |                        | `false`                    | send verbose notifications                                             |
| `zed__notify.zfs_data_event`           | bool   | ❌       |                        | `false`                    | send notifications for `ereport.fs.zfs.data`                           |
| `zed__notify.interval`                 | int    | ❌       | seconds                | `false`                    | notification interval                                                  |
| `zed__enclosure.use_leds`              | bool   | ❌       |                        | `true`                     | turn on/off enclosure LEDs when drives get DEGRADED/FAULTED            |
| `zed__scrub.after_resilver`            | bool   | ❌       |                        | `false`                    | scrub after resilver                                                   |
| `zed__syslog.use_guids`                | bool   | ❌       |                        | `false`                    | use GUIDs in syslog                                                    |
| `zed__syslog.subclass_include.enabled` | bool   | ❌       |                        | `false`                    | enable `SUBCLASS_INCLUDE` for syslog (conflicts with SUBCLASS_EXCLUDE) |
| `zed__syslog.subclass_include.content` | string | ❌       |                        | `checksum|scrub_*|vdev.*`  | included subclasses for syslog                                         |
| `zed__syslog.subclass_exclude.enabled` | bool   | ❌       |                        | `true`                     | enable `SUBCLASS_EXCLUDE` for syslog (conflicts with SUBCLASS_INCLUDE) |
| `zed__syslog.subclass_exclude.content` | string | ❌       |                        | `history_event`            | excluded subclasses for syslog                                         |


## 💻 Example Usage

```yaml
---
# group_vars
zed__notify:
  verbose: true
  zfs_data_event: true
  interval: 3600

---
# Playbook
- name: Proxmox Virtual Environment
  hosts: proxmox
  remote_user: "{{ remote_user__admin.user }}"
  tasks:

    - name: Configure Linux system
      tags:
        - linux_system
      block:

...

        - name: Configure ZFS event daemon
          ansible.builtin.import_role:
            name: zed
          tags:
            - zed
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
