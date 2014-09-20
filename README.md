Start a simple web development environment with PuPHPet ready in 5 minutes!
==========================

That **PuPHPet configuration file** is usefull for simply start a faster web development environment with **debugging tools** under a *LAMP* architecture (Linux/Apache/MySQL/PHP) using **Vagrant**.

The finaly generated box provide all packages and Apache/PHP/Ruby/Pear/PECL modules what you need in major cases for web developments including debugging and admin tools (Xdebug, Drush, Composer, multitail, ccze...)

**Firstly writed and optimized for a Windows platform** *(specially for sharing web root folder settings using NFS or RSYNC)*, succesfully tested with Windows 8.1 x64, VirtualBox 4.3.16 and Vagrant 1.6.5 (and Cygwin x64 1.7+ for Rsync sharing type).

## How-to use?

## Required:
- VirtualBox 4.3+ OR VmWare Workstation 10+
- Vagrant 1.6+
  - plugin(s):
    - winnfs (only if you use the nfs sharing type)
- Cygwin 1.7+ (Windows - only if you use the rsync sharing type)
  - packages:
    - rsync

## Ready?
Go to [PuPHPet website](https://puphpet.com) and drag the PuPHPet-generated manifest *(config.yaml)* from this repository into your browser, the config file will be loaded and ready for edit with! You can edit config on GUI or directly click on create button to generate and download archive.

Next, extract the downloaded archive, next go into this folder and type:

``` shell
$ vagrant up
```

Enjoy!

## Default configuration

### Mysql:

You can put your (MySql) dump at "./_sql/dbname.sql" it will be automatically imported into "dbname" database.

- root password:  rootpassword

- name:       dbname
- host:       localhost
- user:       dbuser
- password:   dbpassword

Note: Connect using SSH tunnel (example: MySQL Workbench (Cross Platform)), username vagrant and SSH key generated at puphpet/files/dot/ssh/id_rsa. This key is generated after your initial "$ vagrant up"! 

### Apache vhost:

Put your files into "./_webfiles", it's the public web root.

- server_name: dev.comexpertise.com
- www_root: /var/www/_webfiles
- listen_port: '80'
- location: \.php$
- index_files:
  - index.html
  - index.htm
  - index.php
  envvars:
      - 'APP_ENV dev'

## Choose sharing type

### WINNFS (Vagrant plugin, default option)
This actually configured for use NFS shares for speed improvment under Windows. For use this Vagrant configuration file you have to installed the [winnfs vagrant plugin](https://github.com/GM-Alex/vagrant-winnfsd), you can easily install from console (for Windows users, we recommand to use Cygwin or PowerShell), simply run:
```
$ vagrant plugin install vagrant-winnfsd
```

Note: for use VirtualBox under Windows you have to uninstall the Hyper-V feature.

Read: [this post](http://mitchellh.com/comparing-filesystem-performance-in-virtual-machines Another good (fast)) solution under Windows is to use RSYNC fonctionnality for "sharing" files, for use it simply change settings in config file and replace "nfs" with "rsync" in synced_folder > sync_type.

### RSYNC
Rsync if faster more than NFS under Windows, it simply use VM local filesystem but synchronize your locally (host) files changed.

For use it, you can simply use [Cygwin](https://www.cygwin.com/) and install the rsync packages and the dependencies. Next you have to edit the config.yaml file and replace "sync_type: nfs" with "sync_type: rsync".

Note about ownership: You need to manually edit the Vagrantfile to force owner/group set after rsync finished. Simply add "group: 'www-data', owner: 'www-data', " before "rsync__args: rsync_args, rsync__exclude:", finally done:

``` yaml
# in Vagrantfile at line 81:
data['vm']['synced_folder'].each do |i, folder|
      ...
        config.vm.synced_folder "#{folder['source']}", "#{folder['target']}", id: "#{i}",
          group: 'www-data', owner: 'www-data', rsync__args: rsync_args, rsync__exclude: rsync_exclude, rsync__auto: rsync_auto, type: 'rsync'
      else
        ...
      end
    end
  end
```

## Need more speed?

- An good solution for performances improvement under Windows are to switch from VirtualBox to [VMWare](http://www.vmware.com/) (with [Vagrant VMWare plugin](https://www.vagrantup.com/vmware) (not free)).
- Another solution (best performances) need to disable Vagrant sharing folders for use local filesystem of VM, and configure your favorite IDE/FTP for use FSTP (SSH) and simply upload modified files (Netbeans can automatically upload new created/modified files)

## Box system details:
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

