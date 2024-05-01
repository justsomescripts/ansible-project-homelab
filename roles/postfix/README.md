# Role -  postfix

Use [Postfix](https://www.postfix.org/)'s relay functionality to send (critical) system information using an existing SMTP server.

## 📋 Requirements

* [ansible.builtin.package](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/package_module.html)
* [ansible.builtin.template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
* [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
* [ansible.builtin.command](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html)

## 🧩 Variables

| name                                                  | type   | required | choices                    | default                              | description                                    |
| ----------------------------------------------------- | ------ | -------- | -------------------------- | ------------------------------------ | ---------------------------------------------- |
| `postfix__relay.fqdn`                   | string | ✅       | FQDN                       | `smtp.example.com`                   | SMTP relay server to use for sending mails     |
| `postfix__relay.port`                   | int    | ❌       | port                       | `465`                                | SMTP port, default works for TLS               |
| `postfix__sender.mail`                  | string | ✅       | mail address               | `mail@example.com`                   | SMTP sender mail                               |
| `postfix__sender.name`                  | string | ✅       | human readable name        | `name`                               | name used in the `From` section of mail header |
| `postfix__sender.local_user`            | string | ❌       | Linux user                 | `root`                               | system user for canonical sender mapping       |
| `postfix__sender.canonical_file`        | string | ❌       | path                       | `/etc/postfix/sender_canonical`      | file to use for canonical sender mapping       |
| `postfix__sender.alias_file`            | string | ❌       | path                       | `/etc/aliases`                       | file to use for mail aliases                   |
| `postfix__sender.header_checks_file`    | string | ❌       | path                       | `/etc/postfix/smtp_header_checks`    | file to use for setting `From` header          |
| `postfix__smtp.credentials.username`    | string | ✅       | username                   | `name@example.com`                   | SMTP username, often similar to mail address   |
| `postfix__smtp.credentials.password`    | string | ✅       | password                   | `changeme`                           | SMTP password                                  |
| `postfix__smtp.credentials.file`        | string | ❌       | path                       | `/etc/postfix/smtp_credentials`      | file to use for storing credentials            |
| `postfix__smtp.credentials.options`     | string | ❌       | Postfix main config option | `noanonymous`                        | `smtp_sasl_security_options`                   |
| `postfix__smtp.tls.enabled`             | bool   | ❌       |                            | `true`                               | `smtp_sasl_security_options`                   |
| `postfix__smtp.tls.enforced`            | bool   | ❌       |                            | `true`                               | `smtp_sasl_security_options`                   |
| `postfix__smtp.tls.security_level`      | string | ❌       |                            | `encrypt`                            | `smtp_sasl_security_options`                   |
| `postfix__smtp.tls.wrappermode_enabled` | bool   | ❌       | Postfix main config option | `true`                               | `smtp_tls_wrappermode`                         |
| `postfix__smtp.tls.ca_file`             | string | ❌       | path                       | `/etc/ssl/certs/ca-certificates.crt` | `smtp_sasl_security_options`                   |

## 💻 Example Usage

```yaml
---
# group_vars
postfix__relay:
  fqdn: smtp.mailbox.org
  port: 465

postfix__sender:
  username: mail@dgries.de
  mail: infra@dgries.de
  name: "{{ ansible_hostname }}"
  local_user: root
  canonical_file: /etc/postfix/sender_canonical
  alias_file: /etc/aliases
  header_checks_file: /etc/postfix/smtp_header_checks

postfix__smtp:
  credentials:
    username: "{{ lookup('community.general.bitwarden', 'Infrastructure Mail', field='username')[0] }}"
    password: "{{ lookup('community.general.bitwarden', 'Infrastructure Mail', field='password')[0] }}"
    file: /etc/postfix/smtp_credentials
    options: noanonymous
  tls:
    enabled: true
    enforced: true
    wrappermode_enabled: true
    security_level: encrypt
    ca_file: /etc/ssl/certs/ca-certificates.crt

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

        - name: Configure Postfix
          ansible.builtin.import_role:
            name: postfix
          tags:
            - postfix
...
```

## 📜 License

See `LICENSE`

## ✍️ Author

David Gries <<mail@dgries.de>>
