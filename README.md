pg_cert_store
=========

Installs and configures [pg-cert-store](https://github.com/cosandr/pg-cert-store).

Requirements
------------

A PostgreSQL database and credentials to connect to it.

Role Variables
--------------

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `pg_cert_config_path` | /etc/pg-cert-store | Path to config files |
| `pg_cert_default_cert_path` | /etc/pki/tls/certs | Where to place pulled certs if they're missing `cert_path` |
| `pg_cert_default_key_path` | /etc/pki/tls/private | Where to place pulled private keys if they're missing `key_path` |
| `pg_cert_default_owner` | root | Owner of certificate files if `owner` is missing |
| `pg_cert_default_group` | root | Group of certificate files if `group` is missing |
| `pg_cert_pull` | [] | List of pull configs, see example playbook |
| `pg_cert_pg_host` | "" | Hostname/IP of PG server |
| `pg_cert_pg_db` | certs | Database name to connect to |
| `pg_cert_pg_user` | certs_pusher | PG user to connect as |
| `pg_cert_pg_password` | "" | Password of PG user |
| `pg_cert_pg_schema` | public | Schema name to be used |
| `pg_cert_pull_oncalendar` | `*-*-* 04:00:00` | systemd timer OnCalendar to use, default is daily at 04:00 (4am) |
| `pg_cert_pull_hooks` | [] | List of dicts defining hooks, see playbook |
| `pg_cert_hooks_dir` | /etc/pg-cert-store/hooks.d | Path to deploy hooks |
| `pg_cert_pull_hooks_sync` | true | Whether or not to delete hooks no longer defined in `pg_cert_pull_hooks` |

Example Playbook
----------------

```yml
- hosts: servers
  roles:
    - role: cosandr.pg_cert_store
      vars:
        pg_cert_pull:
          # All details
          - name: "example.com"
            cert_path: "/etc/nginx/example.crt"
            key_path: "/etc/nginx/example.key"
            owner: root
            group: nginx
          # If cert_path or key_path are not specified
          # cert is placed at "{{ pg_cert_default_cert_path }}/{{ c.name }}.crt"
          # key is placed at "{{ pg_cert_default_key_path }}/{{ c.name }}.key"
          - name: "another.example.com"

        pg_cert_pull_hooks:
          - name: restart-nginx
            content: |
              #!/bin/sh

              systemctl restart nginx
```

License
-------

MIT

Author Information
------------------

Andrei Costescu
