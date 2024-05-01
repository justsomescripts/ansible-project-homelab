# Role -  pve_user

Create [PVE users](https://pve.proxmox.com/pve-docs/chapter-pveum.html) and assign privileges. Supports both `PAM` and `PVE` realms.

## 📋 Requirements

* [ansible.builtin.command](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html)

## 🧩 Variables

| name                                     | type   | required | choices                | default            | description                              |
| ---------------------------------------- | ------ | -------- | ---------------------- | ------------------ | ---------------------------------------- |
| `pve_user__user.create`    | bool   | ❌       |                        | `true`             | create user if it doesn't exist          |
| `pve_user__user.name`      | string | ✅       | username without realm | `user`             | name of the user                         |
| `pve_user__user.password`  | string | ✅       | password               | `changeme`         | password of the user                     |
| `pve_user__user.realm`     | string | ❌       | `pve`<br>`pam`         | `pve`              | PVE realm (use PAM for CLI user)         |
| `pve_user__user.groups`    | array  | ❌       | group name             | `["vmadmin"]`      | assign (existing) group                  |
| `pve_user__user.expire`    | int    | ❌       | time in seconds        | `0` (unlimited)    | time since epoch                         |
| `pve_user__user.firstname` | string | ✅       | display name           | `Firstname`        | display name of the user                 |
| `pve_user__user.lastname`  | string | ✅       | display name           | `Lastname`         | display name of the user                 |
| `pve_user__user.comment`   | string | ❌       | comment                | `generic user`     | user comment                             |
| `pve_user__user.mail`      | string | ✅       | mail address           | `mail@example.com` | e-mail address of the user               |
| `pve_user__group.create`   | bool   | ❌       |                        | `true`             | create group if it doesn't exist         |
| `pve_user__group.name`     | string | ✅       | group name             | `vmadmin`          | name of the group                        |
| `pve_user__group.comment`  | string | ❌       | comment                | `generic user`     | group comment                            |
| `pve_user__acl.create`     | bool   | ❌       |                        | `true`             | create ACL binding if it doesn't exist   |
| `pve_user__acl.path`       | string | ❌       | path                   | `/`                | allow access to that path                |
| `pve_user__acl.roles`      | array  | ✅       | role name              | `["PVEVMAdmin"]`   | role assigned to ACL                     |
| `pve_user__acl.propagate`  | bool   | ❌       |                        | `true`             | permission propagation (inheritance)     |
| `pve_user__acl.groups`     | array  | ✅       | group name             | `["vmadmin"]`      | group assigned to ACL (`false` for none) |
| `pve_user__acl.groups`     | array  | ✅       | user name              | `false`            | usersassigned to ACL (`false` for none)  |

## 💻 Example Usage

```yaml
---
# host_vars
pve_user__user:
  create: true
  name: admin
  password: "{{ lookup('community.general.bitwarden', 'Proxmox VE admin', field='password')[0] }}"
  realm: pam
  groups:
    - vmadmin
  expire: 0
  firstname: David
  lastname: Gries
  comment: VM administration
  mail: infra@dgries.de

---
# Playbook
- name: Proxmox Virtual Environment
  hosts: proxmox
  remote_user: "{{ remote_user__admin.user }}"
  tasks:

    - name: Configure PVE
      tags:
        - pve_system
      block:

        - name: Configure PVE admin user
          ansible.builtin.import_role:
            name: pve_user
          tags:
            - pve_user
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
