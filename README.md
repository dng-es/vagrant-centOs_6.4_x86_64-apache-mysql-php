vagrant-centOs_6.4_x86_64-apache-mysql-php
==========================================

Vagrant centOs 6.4_x86_64 with Apache, Mysql and Php

<b>Vagrantfile</b>
```shell 
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "CentOs6.4_x86_64"
  config.vm.box_url = "https://github.com/2creatives/vagrant-centos/releases/download/v6.4.2/centos64-x86_64-20140116.box"

  config.vm.provision :shell, path: "bootstrap.sh"
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 3306, host: 3307
end


# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

```

<b>bootstrap.sh</b>
```shell 
#!/usr/bin/env bash

sudo yum update -y
sudo yum install httpd -y
if ! [ -L /var/www/html ]; then
  sudo rm -rf /var/www/html
  sudo ln -fs /vagrant/httpdocs /var/www/html
fi

#install MYSQLSERVER
sudo yum install mysql-server -y
sudo service mysqld start
if [ ! -f /var/log/databasesetup ];
then
    echo "CREATE USER 'my_user'@'localhost' IDENTIFIED BY 'my_user_pass'" | mysql -u root
    echo "CREATE USER 'my_user'@'%' IDENTIFIED BY 'my_user_pass'" | mysql -u root
    echo "CREATE DATABASE my_database" | mysql -u root
    echo "GRANT ALL ON my_databse.* TO 'my_user'@'localhost'" | mysql -u root
    echo "GRANT ALL ON my_database.* TO 'my_user'@'%'" | mysql -u root
    echo "flush privileges" | mysql -u root

    touch /var/log/databasesetup

    if [ -f /vagrant/bbdd.sql ];
    then
        mysql -u root my_database < /vagrant/bbdd.sql
    fi
fi

# install PHP5
sudo yum install php php-mysql -y
 
# restart services
sudo service httpd start
sudo service mysqld start
sudo service iptables stop

```
