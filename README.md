# practical-ansible
[![CircleCI](https://circleci.com/gh/spetex/practical-ansible/tree/master.svg?style=svg)](https://circleci.com/gh/spetex/practical-ansible/tree/master)

Hello, this is a practical setup for personal infrastructure.

## Goal
Create ansible setup for secure server with ownCloud, webserver (nginx), LDAP and Postfix mail server.

## Roles
For this purpose the stack will contain some roles.

-   common (iptables, fail2ban, notifying about behavior using mail)
-   Encryption with Lets Encrypt
-   webserver (nginx)
-   cloud (php, mysql/postgress and owncloud server)
-   OpenLDAP
-   Mailserver with Postfix and Dovecot


# Install

## General requirements

Please don't forget to install python on your target host. It's quite common mistake. AWS EC2 instances do not have python installed by default.

1. Your first step should be to update all packages on target host. You can use update.yml for that.

## OpenLDAP role

Installs Open LDAP (slapd) as service on specified nodes and configures its structure and permissions to identify users and services among multiple domains.

Define `organizations` in your playbook or inventory as array of domains and the role will configure the directory structure for all of them.

You also need to define `base_domain` to keep your passwords stored in password storage.

### Example

```
# playbook.yml
---
- name: Setup LDAP
  hosts: all
  become: yes
  vars:
    base_domain: my.custom.ldap.domain.com
    organizations:
      - example.com

  roles:
    - ldap

```

```
# LDAP Structure
dc=ldap
├─ ou=admin
├─ ou=services
└─ dc=example.com
   ├─ ou=groups
   └─ ou=users
```

## Mail role

Installs Postfix and Dovecot and configures them to act together as IMAP/SMTP mail server with virtual database of mailboxes and domains provided by OpenLDAP. Uses Lets Encrypt to generate certificates for communication.

You need to define `{{base_domain}}` to keep your passwords stored in password storage. Hostname of your mail server will be configured as `mail.{{base_domain}}` unless you override variable `{{mail_host}}`.

### Manual configuration

Ansible role is not able to configure DNS for you. You will need to configure few DNS records.

#### DNS Before playing role

* an A and/or AAAA with `{{mail_host}}` pointing to the IP address of mail server, it is required to configure Letsencrypt certificates

#### After playing role

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

### Client configuration

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

## EC2 Management and Provisioning Notes

In order to run `amazon.yml` EC2 provisioning playbook you need to install and configure [boto](https://github.com/boto/boto3boto) python library.

Also pay attention to the `vars` section where you need to specify the instance type, image, aws region, etc... for instances you want to spawn.
