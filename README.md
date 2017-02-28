# Vagrant LAMP with Ansible

This is a LAMP development environment that uses Vagant and Ansible to start developing in less than 5 minutes.


## Readme Index

- [Prerequisites](https://github.com/Mayccoll/vagrant-lamp#prerequisites)
- [What is included?](https://github.com/Mayccoll/vagrant-lamp#what-is-included)
- **[Quick Start](https://github.com/Mayccoll/vagrant-lamp#quick-start-checkered_flag)**
- [Fast Configuration](https://github.com/Mayccoll/vagrant-lamp#fast-configuration)
- [Advanced Configuration](https://github.com/Mayccoll/vagrant-lamp#advanced-configuration)
- [Ansible](https://github.com/Mayccoll/vagrant-lamp#ansible)
- [PhpMyAdmin](https://github.com/Mayccoll/vagrant-lamp#phpmyadmin)
- [LAMP](https://github.com/Mayccoll/vagrant-lamp#lamp)
- **[Create a SSL Certificate](https://github.com/Mayccoll/vagrant-lamp#create-a-ssl-certificate)**



## Prerequisites

You'll need to have the following prerequisites **installed** on your workstation:

* [VirtualBox](https://www.virtualbox.org/)
* [Vagrant](http://www.vagrantup.com/)
* [Ansible](http://www.ansibleworks.com)
* Add an appropriate vagrant box (optional)
```bash
vagrant box add bento/ubuntu-16.04
```

## What is included?

- **PHP 7**
- **Apache**
- **MySql**
- **PhpMyAdmin**
- Unzip
- Curl
- Git
- Zsh
- Oh-My-ZSH
- Terminal Fonts

## Quick Start :checkered_flag:


```bash
  $ git clone git@github.com:Mayccoll/vagrant-lamp.git
  $ cd vagrant-lamp
  $ vagrant up

  open http://192.168.90.10
```

**DONE!!!**

Once the process is finished you can place your application in the new ```html``` folder.


**Optional:**
you can add the next rule to your host file and go to  http://lamp.local

```bashbash
192.168.90.10     lamp.local
```

use this command line to add the rule:

```bash
$ echo "\n192.168.90.10     lamp.local" | sudo tee -a /etc/hosts
```

-----


## Fast Configuration

For fast configuration you can modify this variables in ```CONFIG.yml``` file.

```ỳaml
# |                              ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
local_domain : &local_domain    'lamp.local'
private_ip   : &private_ip      '192.168.90.10'
machine_name : &machine_name    'vag-lamp'
machine_ram  : &machine_ram     'auto'
machine_cpu  : &machine_cpu     'auto'
# |                              ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
```

## Advanced Configuration

There are one configuration file ```CONFIG.yml```.

```bash
.
└── CONFIG.yml

```

#### Config VirtualBox VM

  > **./CONFIG.yaml**

```yaml
- name               : vag-lamp
  box                : bento/ubuntu-16.04
  box_version        : 2.3.1
  box_check_update   : false
  ram                : 1028
  cpus               : 1
```

#### Defining a Forwarded Port

  > **./CONFIG.yaml**

```yaml
ports           :
  - guest       : 3000
    host        : 3000

  - guest       : 80
    host        : 8080
```

#### Config Synced folders

  > **./CONFIG.yaml**

```yaml
  syncDir         :
    - host        : share
      guest       : /home/vagrant/share

    - host        : www
      guest       : /home/vagrant/www
      owner       : vagrant
      group       : vagrant
      dmode       : 775
      fmode       : 775
```


## Ansible


#### Ansible Playbook

This playbook will install all the dependencies for LAMP.

It can be found in:

  > **./ansible/playbook.yaml**


## PhpMyAdmin

http://192.168.90.10:8888

- **Username:** vagrant
- **Password:** vagrant


##  Create a SSL Certificate

- Go into vagrant vm

```
$ vagrant ssh
```

- Create ssl Dir

```bash
$ sudo mkdir /etc/apache2/ssl
```

- Create ssl certificate

```bash
$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
```

- Add Virtual Host in the ```vhost.conf``` file

```bash
$ sudo vim /etc/apache2/sites-available/vhost.conf
```

```xml
<VirtualHost *:443>
    ServerAdmin webmaster@localhost

    DocumentRoot /var/www/html
    <Directory /var/www/html/>
        Options -Indexes +Includes +FollowSymLinks +Multiviews
        AllowOverride All
        Order allow,deny
        allow from all
    </Directory>

    ErrorLog /var/log/apache2/error.log
    CustomLog /var/log/apache2/access.log combined

    SSLEngine on
    SSLCertificateFile  /etc/apache2/ssl/apache.crt
    SSLCertificateKeyFile  /etc/apache2/ssl/apache.key
    SetEnvIf User-Agent ".*MSIE.*" nokeepalive ssl-unclean-shutdown
</VirtualHost>
```

- Enable ssl mod

```bash
$ sudo a2enmod ssl
```

- Restart Apache

```bash
$ sudo service apache2 restart
```

- Go to:

    - https://192.168.90.10
    - https://lamp.local (optional)
