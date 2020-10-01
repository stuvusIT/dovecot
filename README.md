# dovecot

This role sets up a Dovecot installation with most things already preconfigured.
It has support for ACLs, sieve, LDAP, and automatic reporting to rspamd.
Also, clustering via dsync is supported.

The log is redirected to the systemd journal and the service is hardened using some systemd sandboxing.

## Requirements

Debian

## Role Variables

| Name                              | Default / Mandatory     | Description                                                                              |
| :-------------------------------- | :---------------------- | :--------------------------------------------------------------------------------------- |
| `dovecot_release`                 | `2.3`                   | Release of Dovecot to install (relevant for the apt repository)                          |
| `dovecot_mail_location`           | `/var/lib/imap`         | Location for all mail-related files                                                      |
| `dovecot_vmail_user`              | `vmail`                 | User which will own all mail                                                             |
| `dovecot_vmail_group`             | `vmail`                 | Group which will own all mail                                                            |
| `dovecot_dhparam`                 | :heavy_check_mark:      | Location of the TLS dhparam file                                                         |
| `dovecot_ca_dir`                  | `/etc/ssl/certs`        | Location of the CA certificates directory                                                |
| `dovecot_cert`                    | :heavy_check_mark:      | IMAP/managesieve certificate                                                             |
| `dovecot_key`                     | :heavy_check_mark:      | IMAP/managesieve private key                                                             |
| `dovecot_dsync_tls_cert`          | (:heavy_check_mark:)    | (Only if using dsync) Path to the dsync certificate                                      |
| `dovecot_dsync_tls_key`           | (:heavy_check_mark:)    | (Only if using dsync) Path to the dsync key                                              |
| `dovecot_listen`                  | `[*, ::]`               | IPs/ports to listen on                                                                   |
| `dovecot_default_client_limit`    | `400`                   | Default client limit for services                                                        |
| `dovecot_tls`                     | `required`              | Whether TLS should be offered to clients                                                 |
| `dovecot_tls_min`                 | `TLSv1.2`               | Minimum TLS version to expect from clients                                               |
| `dovecot_tls_ciphers`             | (see defaults/main.yml) | List of TLS ciphers to allow for users                                                   |
| `dovect_disable_plain`            | `true`                  | Whether authentication should be disallowed when using a plain connection                |
| `dovecot_auth_worker_max_count`   | `350`                   | Maximum authentication workers to spawn                                                  |
| `dovecot_auth_worker_proclimit`   | `350`                   | Process limit for the auth workers                                                       |
| `dovecot_ldap_hosts`              | `[]`                    | List of LDAP host names                                                                  |
| `dovecot_ldap_uris`               | `[]`                    | List of LDAP host URIs                                                                   |
| `dovecot_ldap_auth_bind`          | `true`                  | Whether Dovecot should bind for auth                                                     |
| `dovecot_ldap_blocking`           | `true`                  | Whether Dovecot should block while performing LDAP searches                              |
| `dovecot_ldap_tls`                | `no`                    | Whether LDAP should use TLS                                                              |
| `dovecot_ldap_cert_file`          | `null`                  | Path to the LDAP certificate file                                                        |
| `dovecot_ldap_cert_dir`           | `null`                  | Path to a LDAP certificate directory                                                     |
| `dovecot_ldap_require_cert`       | `never`                 | When to require an LDAP certificate                                                      |
| `dovecot_ldap_dn`                 | :heavy_check_mark:      | LDAP DN to bind with                                                                     |
| `dovecot_ldap_dn_pass`            | :heavy_check_mark:      | Password of the LDAP DN to bind with                                                     |
| `dovecot_ldap_base`               | :heavy_check_mark:      | LDAP search base                                                                         |
| `dovecot_ldap_scope`              | `subtree`               | LDAP search scope                                                                        |
| `dovecot_ldap_pass_attrs`         | `[uid=user]`            | LDAP-Dovecot attribute-mapping to maintain                                               |
| `dovecot_ldap_pass_filter`        | :heavy_check_mark:      | LDAP filter for all users                                                                |
| `dovecot_ldap_master_pass_filter` | `null`                  | LDAP filter for master users. Setting this to null disables the master feature           |
| `dovecot_dsync`                   | `false`                 | Whether to enable the dsync functionality                                                |
| `dovecot_dsync_max_connections`   | `10`                    | Maximum parallel dsync connections to spawn                                              |
| `dovecot_dsync_port`              | `12345`                 | Dsync port to use                                                                        |
| `dovecot_dsync_password`          | (:heavy_check_mark:)    | (Only if using dsync) Shared secret to establish dsync connection                        |
| `dovecot_dsync_parameters`        | `-d -N -l 3000 -U`      | Parameters to set for dsync child processes                                              |
| `dovecot_dsync_sync_timeout`      | `null`                  | Set to a non-null value to enable synchronous replication                                |
| `dovecot_dsync_target`            | `null`                  | Dsync target to replicate with                                                           |
| `dovecot_imap_hibernate_timeout`  | `5s`                    | Time for clients to idle before being pushed to the hibernation process                  |
| `dovecot_imap_process_limit`      | `100`                   | Maximum number of IMAP processes                                                         |
| `dovecot_imap_process_min_avail`  | `4`                     | Minimum number of available IMAP processes to accept connections                         |
| `dovecot_private_prefix`          | `null`                  | Prefix for the private dovecot namespace                                                 |
| `dovecot_private_mailboxes`       | `{}`                    | Mailbox configurations for the private mailboxes (see below)                             |
| `dovecot_rspamd_connect`          | `null`                  | Rspamd host to connect to for reporting spam                                             |
| `dovecot_rspamd_password`         | `null`                  | Rspamd password to use when reporting spam                                               |
| `dovecot_fts_autoindex`           | `true`                  | Whether to enable autoindexing for new mails                                             |
| `dovecot_protected_mailboxes`     | `[]`                    | List of folder names that should be non-deletable and impossible to create subfolders of |
| `dovecot_extra_acls`              |                         | Extra text to append to the ACLs file                                                    |

### mailbox configuration
| Name          | Default / Mandatory | Description                                                                             |
| :------------ | :------------------ | :-------------------------------------------------------------------------------------- |
| `special_use` |                     | IMAP special-use flag                                                                   |
| `auto`        |                     | `auto` value for Dovecot                                                                |
| `autoexpunge` |                     | Time after which mails are automatically expunged (is applied to the IMAP service only) |

## Example Playbook

```yml
- hosts: imap
  roles:
    - role: dovecot
      dovecot_dhparam: /etc/ssl/dhparam.pem
      dovecot_key: /etc/ssl/dovecot.key
      dovecot_cert: /etc/ssl/dovecot.crt
      dovecot_dsync_tls_key: /etc/ssl/dovecot.key
      dovecot_dsync_tls_cert: /etc/ssl/dovecot.crt
      dovecot_protected_mailboxes:
        - INBOX.Spam
        - INBOX.Trash
      dovecot_ldap_uris:
        - ldaps://ldap.example.com
      dovecot_ldap_base: dc=example,dc=com
      dovecot_ldap_dn: uid=root,dn=example,dc=com
      dovecot_ldap_dn_pass: qua3fuTiudahneiNeiph7Theexaecooboop9uphaezaesh4ieg
      dovecot_ldap_pass_filter: (uid=%u)
      dovecot_dsync: true
      dovecot_dsync_password: Aeng8ie9eit2ohfeiJi1koozirohbo2Poh3iej2eizoo1chaub
      dovecot_private_prefix: INBOX.
      dovecot_private_mailboxes:
        Drafts:
          special_use: '\Drafts'
          auto: subscribe
        Sent:
          special_use: '\Sent'
          auto: subscribe
        Junk:
          special_use: '\Junk'
          auto: subscribe
          autoexpunge: 30d
        Trash:
          special_use: '\Trash'
          auto: subscribe
          autoexpunge: 90d
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).

## Author Information

- [Janne Heß](https://github.com/dasJ)
