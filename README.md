# practical-ansible

Hello, this is a practical setup for personal infrastructure.

## Goal
Create ansible setup for secure server with ownCloud, webserver (nginx), LDAP and Postfix mail server.

## Roles
For this purpose the stack will contain some roles.

- common (iptables, fail2ban, notifying about behavior using mail)
- webserver (nginx, letsencrypt)
- cloud (php, mysql/postgress and owncloud server)
- authentication (OpenLDAP?)
- mail (Postfix?)
