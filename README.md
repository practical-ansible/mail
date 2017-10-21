# Practical Ansible Mail

Installs Postfix and Dovecot and configures them to act together as IMAP/SMTP mail server with virtual database of mailboxes and domains provided by OpenLDAP. Uses Lets Encrypt to generate certificates for communication.

You need to define `{{base_domain}}` to keep your passwords stored in password storage. Hostname of your mail server will be configured as `mail.{{base_domain}}` unless you override variable `{{mail_host}}`.

## Manual configuration

Ansible role is not able to configure DNS for you. You will need to configure few DNS records.

### DNS Before playing role

* an A and/or AAAA with `{{mail_host}}` pointing to the IP address of mail server, it is required to configure Letsencrypt certificates

### After playing role

* TXT record with your DKIM key, you need this to sign your e-mails
* TXT record with your DKIM version, you need this to sign your e-mails
* PTR record, some mail servers will not talk to you if you skip this

For each organization, you will need to configure a TXT record with Open DKIM key. You will find the DKIM keys in folder `remote/{{organization}}`. The file might look like this:

```

mail._domainkey IN      TXT     ( "v=DKIM1; k=rsa; "
          "p=VERY_LONG_SECRET_KEY" )  ; ----- DKIM key mail for example.com
```

Your TXT records would look like this:

```
TXT "mail._domainkey.example.com" "v=DKIM1; k=rsa; p=VERY_LONG_SECRET_KEY"
TXT "example.com" "v=spf1 mx a -all"
```

## Client configuration

To configure your e-mail client use following settings:

```
IMAP
----
host: {{mail_host}}:143
user: user@{{organization}}
encryption: STARTTLS

SMTP
----
host: {{mail_host}}:587
user: user@{{organization}}
encryption: STARTTLS
```
