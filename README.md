A web development environnement with PuPHPet
==========================

That PuPHPet configuration file is usefull for simply start a web development environment with debugging tools under a LAMP architecture (Linux/Apache/MySQL/PHP) using Vagrant.

The finaly generated box provide all packages and Apache/PHP/Ruby/Pear/PECL modules what you need in major cases for web developpements including debugging tools.

Firstly writed and optimized for a Windows platform (specially for sharing web root folder settings), succesfully tested with Windows 8.1x64, VirtualBox 4.3.16 and Vagrant 1.6.5.

## Box configuration:

#### System:
- memory: 512
- cpus: 2
- private_network: 192.168.56.100
- forwarded_port: 6242 > 22

#### Shared folder (with your host, type: nfs):
- ./ (your vagrant folder but ".vagrant/" excluded) > /var/www

#### OS:
- Debian Wheezy 7.5 x64
  - packages:
    - build-essential
    - libmagickwand-dev
    - curl
    - git-core
    - php-console-table
    - htop
    - vim
    - ccze
    - multitail
    - ghostscript
- Apache (www-data:www-data
  - modules:
    - rewrite
    - expires
    - headers
    - mime_magic
    - ssl
    - deflate
- PHP 5.5
  - modules:
    - php:
      - cli
      - intl
      - mcrypt
      - curl
      - gd
      - imap
      - mbstring
      - tidy
      - xmlrpc
      - common
      - cgi
      - sqlite
      - recode
    - pear:
    - pecl:
      - pecl_http
      - pdflib
      - uploadprogress
      - imagick
  - php.ini:
      - display_errors: On
      - error_reporting: '-1'
      - session.save_path: /var/lib/php/session
      - max_execution_time: '600'
      - memory_limit: 256M
      - upload_max_filesize: 20M
      - post_max_size: 25M
      - max_input_time: '300'
      - max_input_vars: '2000'
  - timezone: Europe/Paris
  - mod_php: 0
- XDebug actived on port :9000

## Required:
- VirtualBox 4.3+
- Vagrant 1.6+
- Vagrant plugin(s) : winnfs

## WINNFS (Vagrant plugin)
This actually configured for use NFS shares for speed improvment under Windows. For use this Vagrant configuration file you have to installed the winnfs vagrant plugin (https://github.com/GM-Alex/vagrant-winnfsd), you can easily install from console (for Windows users, we recommand to use Cygwin or PowerShell), simply run:
```
$ vagrant plugin install vagrant-winnfsd
```

Note: for use VirtualBox under Windows you have to uninstall the Hyper-V feature.

Read: http://mitchellh.com/comparing-filesystem-performance-in-virtual-machines Another good (fast) solution under Windows is to use RSYNC fonctionnality for "sharing" files, for use it simply change settings in config file and replace "nfs" with "rsync" in synced_folder > sync_type.

## How-to use?

Simply clone this repository and run $ vagrand up. Enjoy!

## How-to edit config?

Simply edit puphpet/config.yaml or use the GUI:

Go to https://puphpet.com website and drag the PuPHPet-generated manifest located in "puphpet/config.yaml into your browser, and the config file will be loaded and ready for edit with!

After, extract the archive, go to this folder and type :

```
$ vagrant up
```

## Parameters

### Mysql:

root password: rootpassword

- name: dbname
- host: localhost
- user: dbuser
- password: dbpassword

Note: Connect using SSH tunnel (example: MySQL Workbench (Cross Platform)), username vagrant and SSH key generated at puphpet/files/dot/ssh/id_rsa. This key is generated after your initial "$ vagrant up"! 

### Apache vhost:
- server_name: awesome.dev
- server_aliases:
  - www.awesome.dev
- www_root: /var/www/awesome
- listen_port: '80'
- location: \.php$
- index_files:
  - index.html
  - index.htm
  - index.php
  envvars:
      - 'APP_ENV dev'
