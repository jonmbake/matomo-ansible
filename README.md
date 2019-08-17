## Secure Matomo Provision

An [Ansible](https://docs.ansible.com/) playbook to provision a secure [Matomo](https://matomo.org/) instance.

With support for local provisioning via Vagrant (useful to quickly testing changes).

Both local and non-local provisions were tested on a [Ubuntu 18.04](http://releases.ubuntu.com/18.04/) instance.

**Blog post:** https://jonbake.com/blog/2019/08/17/setting-up-a-personal-matamo-instance.html

## Provisioning Locally

* Requires [Vagrant](https://www.vagrantup.com/) to be installed.

Simply run:

```
vagrant up
```

Then navigate to https://localhost:8443/. Note: because it is using a self-signed certificate, you will receive a warning when opening the page within a browser.

## Provisioning a Production, i.e. Remote, Site

* Requires [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) to be installed.

First make sure there is a remote _Ubuntu 18.04_ instance that you want to install Matomoo on. Also, make sure you can ssh in, i.e. running the command `ssh root@my-matomo-instace.com` should be successful.

Then make sure to update the inventory's `prod` entry:

```
[prod]
my-matomo-instace.com ansible_user=root
```

### Running the playbook to provision the instance

Certbot requires `certificate_contact_email` and `certificate_domain` to be set. These can be passed as [extra vars](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#id35) when invoking the playbook:

```
ansible-playbook -i inventory site.yml -e "certificate_contact_email=admin@my-matomo-instace.com" -e "certificate_domain=my-matomo-instace.com"
```

## Default Database Credentials

```
db_username: matomo
db_password: 'Jana705&loge'
```

These can be overridden by passing in _extra vars_ like in the above command.


## Other Configuration Notes

Make sure to off the _Archive reports when viewed from the browser_ settings option. The provision creates a cron job to automatically archive reports.

![Archive reports when viewed from the browser off](https://github.com/jonmbake/screenshots/raw/master/matomo-ansible/matomo-archive-reports-off.png)


## Security Features

- UFW Firewall enabled with 443 (SSL) port and rate-limited 22 (SSH) port exposed.
- Uses [Apache](https://httpd.apache.org/) HTTP Server.
- Automatic security upgrades are enabled via [unattended-upgrade](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).
- SSL/HTTPS enable out of the box using [LetsEncrypt/Certbot](https://certbot.eff.org/). SSL is enforced always.
