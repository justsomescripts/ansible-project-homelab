# Role -  ups

Configure an uninterruptible power supply using [NUT](https://networkupstools.org/).

## 📋 Requirements

* [ansible.builtin.template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
* [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
* [ansible.builtin.package](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/package_module.html)
* [ansible.builtin.copy](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html)

## 🧩 Variables

| name                                           | type   | required | choices                                              | default                                         | description                                                                   |
| ---------------------------------------------- | ------ | -------- | ---------------------------------------------------- | ----------------------------------------------- | ----------------------------------------------------------------------------- |
| `ups__user.username`             | string | ❌       | NUT username                                         | `upsmon`                                        | username used by NUT client and server                                        |
| `ups__user.password`             | string | ✅       | NUT password                                         | `changeme`                                      | password used by NUT client and server                                        |
| `ups__nut.mode`                  | string | ❌       | `none`<br>`netclient`<br>`standalone`<br>`netserver` | `standalone`                                    | NUT [mode](https://networkupstools.org/docs/man/nut.conf.html)                |
| `ups__ups.maxretry`              | int    | ❌       |                                                      | `3`                                             | maximum number of connection retries to UPS                                   |
| `ups__ups.name`                  | string | ❌       |                                                      | `upsname`                                       | name of the UPS device                                                        |
| `ups__ups.driver`                | string | ✅       | NUT driver                                           | `ups driver`                                    | name of the [UPS driver](https://networkupstools.org/docs/man/nutupsdrv.html) |
| `ups__ups.port`                  | string | ❌       | linux serial port                                    | `auto`                                          | serial port the device is connected to                                        |
| `ups__upsd.upsmon`               | string | ❌       | `primary`<br>`secondary`                             | `primary`                                       | actions for `upsmon` process (primary = directly attached)                    |
| `ups__upsd.actions`              | string | ❌       | `SET`<br>`FSD`                                       | `SET`                                           | user permissions for upsd                                                     |
| `ups__upsd.instcmds`             | string | ❌       | `ALL` (currently only one can be specified)          | `ALL`                                           | instant commands the user can initiate                                        |
| `ups__upsmon.minsupplies`        | int    | ❌       |                                                      | `1`                                             | minimum required number of power supplies for a system                        |
| `ups__upsmon.shutdowncmd`        | string | ❌       | command                                              | `/sbin/shutdown -h +0`                          | command used to shut down in case of power loss                               |
| `ups__upsmon.pollfreq`           | int    | ❌       | seconds                                              | `5`                                             | number of seconds between UPS status checks                                   |
| `ups__upsmon.pollfreqalert`      | int    | ❌       | seconds                                              | `5`                                             | number of seconds between UPS status checks when on battery                   |
| `ups__upsmon.hostsync`           | int    | ❌       | seconds                                              | `15`                                            | time to wait for sync between primaries and secondaries before force shutdown |
| `ups__upsmon.deadtime`           | int    | ❌       | seconds                                              | `15`                                            | time to wait before declaring a missing UPS "dead"                            |
| `ups__upsmon.powerdownflag`      | string | ❌       | file name                                            | `/etc/killpower`                                | file to be created when UPS needs to be powered off (primary)                 |
| `ups__upsmon.rbwarntime`         | int    | ❌       | seconds                                              | `43200`                                         | notification frequency for battery replacement                                |
| `ups__upsmon.nocommwarntime`     | int    | ❌       | seconds                                              | `300`                                           | warning frequency if communication is not possible                            |
| `ups__upsmon.finaldelay`         | int    | ❌       | seconds                                              | `5`                                             | seconds to wait for shutdown after warning the users                          |
| `ups__upsmon.monitor.system`     | string | ❌       | UPS name                                             | `"{{ ups__ups.name }}@localhost"` | UPS system to monitor                                                         |
| `ups__upsmon.monitor.powervalue` | int    | ❌       |                                                      | `1`                                             | number of power supplies on the system                                        |
| `ups__upsmon.monitor.type`       | string | ❌       | `primary`<br>`secondary`                             | `primary`                                       | monitoring type (primary = directly attached)                                 |


## 💻 Example Usage

```yaml
---
# host_vars
ups__ups:
  maxretry: 3
  name: Eaton3S
  driver: usbhid-ups
  port: auto

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

        - name: Configure UPS
          ansible.builtin.import_role:
            name: ups
          tags:
            - ups
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
