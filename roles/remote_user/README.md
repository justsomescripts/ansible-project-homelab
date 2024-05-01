# Role -  remote_user

Create Linux users, assign privileges and configure remote access over SSH.

## 📋 Requirements

* [ansible.builtin.user](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html)
* [ansible.builtin.template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
* [ansible.builtin.copy](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html)
* [ansible.builtin.stat](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/stat_module.html)
* [ansible.builtin.lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html)
* [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
* [ansible.builtin.package](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/package_module.html)

## 🧩 Variables

| name                                                             | type   | required | choices                 | default                         | description                                |
| ---------------------------------------------------------------- | ------ | -------- | ----------------------- | ------------------------------- | ------------------------------------------ |
| `remote_user__ssh.sshport`                         | int    | ❌       |                         | `22`                            | port used by SSHD service                  |
| `remote_user__user.pubkey_auth_enabled`            | bool   | ❌       |                         | `true`                          | allow SSH public key authentication        |
| `remote_user__user.password_auth_enabled`          | bool   | ❌       |                         | `false`                         | allow SSH password authentication          |
| `remote_user__user.x_forward_enabled`              | bool   | ❌       |                         | `false`                         | enable SSH X forwarding                    |
| `remote_user__user.agent_auth.enabled`             | bool   | ❌       |                         | `true`                          | enable SSH agent sudo privilege escalation |
| `remote_user__user.agent_auth.authorized_keys_dir` | string | ❌       | path                    | `/etc/ssh/sudo_authorized_keys` | path for authorized keys used by agent     |
| `remote_user__admin.user`                          | string | ❌       | Linux username          | `root`                          | linux admin / sudo user                    |
| `remote_user__admin.password`                      | string | ✅       | Linux password          |                                 | password for admin                         |
| `remote_user__admin.update_password`               | string | ❌       | `always`<br>`on_create` | `on_create`                     | when to update password                    |
| `remote_user__admin.create`                        | bool   | ❌       |                         | `false`                         | create user if it doesn't exist            |
| `remote_user__admin.groups`                        | array  | ❌       | list of Linux groups    | `- sudo`<br>`users`             | groups for admin user                      |
| `remote_user__prompt.enabled`                      | bool   | ❌       |                         | `false`                         | customize Bash prompt                      |
| `remote_user__prompt.color_code`                   | int    | ❌       | ANSI color escape code  | `32`                            | customize Bash prompt color                |
| `remote_user__tmux.enabled`                        | bool   | ❌       |                         | `false`                         | customize TMUX status bar                  |
| `remote_user__tmux.color_code`                     | int    | ❌       | TMUX color label        | `green`                         | customize TMUX status bar color            |

## 💻 Example Usage

```yaml
---
# host_vars
remote_user__admin:
  user: "{{ lookup('community.general.bitwarden', 'Proxmox VE admin', field='username')[0] }}"
  create: true
  update_password: always
  password: "{{ lookup('community.general.bitwarden', 'Proxmox VE admin', field='password')[0] }}"
  groups:
    - sudo
    - users

remote_user__ssh:
  sshport: "1337"
  sshkey: "{{ lookup('community.general.bitwarden', 'Proxmox VE admin', field='SSH Public Key')[0] }}\n"
  pubkey_auth_enabled: true
  password_auth_enabled: false
  x_forward_enabled: false
  agent_auth:
    enabled: true
    authorized_keys_dir: /etc/ssh/sudo_authorized_keys

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

        - name: Configure SSH admin user
          ansible.builtin.import_role:
            name: remote_user
          tags:
            - remote_user
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
