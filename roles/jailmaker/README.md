# Role -  jailmaker

Setup [Jailmaker](https://github.com/Jip-Hop/jailmaker/) jails on a TrueNAS scale system

## 📋 Requirements

* [ansible.builtin.stat](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/stat_module.html)
* [ansible.builtin.meta](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/meta_module.html)
* [ansible.builtin.debug](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/debug_module.html)
* [ansible.builtin.get_url](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/get_url_module.html)
* [ansible.builtin.template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
* [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
* [ansible.builtin.command](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html)

## 📜 Prerequisites

Configure a dataset for Jailmaker as described in the ["Configure a Dataset for Jailmaker" section in the official documentation](https://www.truenas.com/docs/scale/24.04/scaletutorials/apps/sandboxes/#configure-a-dataset-for-jailmaker). This is not done by the role to avoid data loss caused by wrong configuration.

The dataset name has to match the `jailmaker__jail.dataset` variable.

## 🧩 Variables

| name                                                        | type           | required | choices                     | default                                                                                                                                                                                                                                                    | description                                                            |
| ----------------------------------------------------------- | -------------- | -------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `jailmaker__jail.name`                        | string         | ✅       | Jailmaker Jail name         | `debian`                                                                                                                                                                                                                                                   | name of the Jail                                                       |
| `jailmaker__jail.dataset`                     | string         | ✅       | Jailmaker dataset           | `tank`                                                                                                                                                                                                                                                     | name of the dataset jailmaker is installed on                          |
| `jailmaker__os.autostart`                     | bool           | ❌       |                             | `true`                                                                                                                                                                                                                                                     | start Jail at boot / Jailmaker startup                                 |
| `jailmaker__os.seccomp_enabled`               | bool           | ❌       |                             | `true`                                                                                                                                                                                                                                                     | enable Seccomp                                                         |
| `jailmaker__os.distribution.name`             | string         | ✅       | supported LXC image         | `debian`                                                                                                                                                                                                                                                   | name of the distribution                                               |
| `jailmaker__os.distribution.release`          | string         | ✅       | supported LXC image         | `bookworm`                                                                                                                                                                                                                                                 | release version of the distribution                                    |
| `jailmaker__os.gpu_passtrough.intel_enabled`  | bool           | ❌       |                             | `false`                                                                                                                                                                                                                                                    | enable Intel GPU passtrough                                            |
| `jailmaker__os.gpu_passtrough.nvidia_enabled` | bool           | ❌       |                             | `false`                                                                                                                                                                                                                                                    | enable Nvidia GPU passtrough                                           |
| `jailmaker__mounts`                           | array of dicts | ❌       | host bind mounts            | `[]`                                                                                                                                                                                                                                                       | host bind mounts (requires `source`=host and `destination`=Jail paths) |
| `jailmaker__systemd.nspawn.user_args`         | array          | ❌       | systemd-nspawn user args    | `- resolv-conf=bind-host`<br>`- system-call-filter='add_key keyctl bpf'`                                                                                                                                                                                   | systemd-nspawn flags passed to Jailmaker config                        |
| `jailmaker__systemd.nspawn.default_args`      | array          | ❌       | systemd-nspawn default args | `- keep-unit`<br>`- quiet`<br>`- boot`<br>`- bind-ro=/sys/module`<br>`- inaccessible=/sys/module/apparmor`                                                                                                                                                 | systemd-nspawn flags passed to Jailmaker config                        |
| `jailmaker__systemd.run.default_args`         | array          | ❌       | systemd-nspawn default args | `- property=KillMode=mixed`<br>`- property=Type=notify`<br>`- property=RestartForceExitStatus=133`<br>`- property=SuccessExitStatus=133`<br>`- property=Delegate=yes`<br>`- property=TasksMax=infinity`<br>`- collect`<br>`- setenv=SYSTEMD_NSPAWN_LOCK=0` | systemd-nspawn flags passed to Jailmaker config                        |

## 💻 Example Usage

```yaml
# Playbook
---
- name: TrueNAS SCALE Jails
  hosts: truenas
  remote_user: "{{ remote_user__admin.user }}"
  tasks:

    - name: Setup container environment
      tags:
        - tn_ctr
      block:

        - name: Setup Jails
          ansible.builtin.import_role:
            name: jailmaker
          tags:
            - tn_jailmaker
          vars:
            jailmaker__jail:
              name: testjail
              dataset: speed
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
