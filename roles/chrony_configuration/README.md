# Role -  chrony_configuration

Configure a local NTP server to be used by [Chrony](https://chrony-project.org/).

## 📋 Requirements

* [ansible.builtin.package](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/package_module.html)
* [ansible.builtin.template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
* [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
* [ansible.builtin.command](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html)

## 🧩 Variables

| name                                    | type   | required | choices       | default | description                             |
| --------------------------------------- | ------ | -------- | ------------- | ------- | --------------------------------------- |
| `chrony_configuration__time.ntp_server` | string | ✅       | IP / hostname |         | set the ntp source to be used by Chrony |

## 💻 Example Usage

```yaml
---
# group_vars
chrony_configuration__time:
  ntp_server: 192.168.178.3

---
- name: Proxmox Virtual Environment
  hosts: proxmox
  remote_user: "{{ remote_user_configuration__admin.user }}"
  tasks:

    - name: Configure Linux system
      tags:
        - linux_system
      block:

      ...

        - name: Configure Chrony
          ansible.builtin.import_role:
            name: chrony_configuration
          tags:
            - chrony_configuration
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
