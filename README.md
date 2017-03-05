# practical-ansible
[![CircleCI](https://circleci.com/gh/spetex/practical-ansible/tree/master.svg?style=svg)](https://circleci.com/gh/spetex/practical-ansible/tree/master)

Hello, this is a practical setup for personal infrastructure.

## Goal
Create ansible setup for secure server with ownCloud, webserver (nginx), LDAP and Postfix mail server.

## Roles
For this purpose the stack will contain some roles.

-   common (iptables, fail2ban, notifying about behavior using mail)
-   webserver (nginx, letsencrypt)
-   cloud (php, mysql/postgress and owncloud server)
-   authentication (OpenLDAP?)
-   mail (Postfix?)

## EC2 Management and Provisioning Notes

In order to run `amazon.yml` EC2 provisioning playbook you need to install and configure [boto](https://github.com/boto/boto3boto) python library.

Also pay attention to the `vars` section where you need to specify the instance type, image, aws region, etc... for instances you want to spawn.
