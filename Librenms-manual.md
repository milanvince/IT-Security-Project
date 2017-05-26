## The following document contains the instructions, how to install Librenms on Ubuntu 16.04 Desktop.

#### First of all check the inet address of the local machine:
```
ifconfig
```

#### Restart mysql:
```
systemctl restart mysql
```
#### Then let's install MySQL. (I have done in root.) 
```
apt-get install mariadb-server mariadb-client
systemctl restart mysql
mysql -uroot -p
```
#### Then create a database:
```
CREATE DATABASE librenms CHARACTER SET utf8 COLLATE utf8_unicode_ci; (Hit Enter)
CREATE USER 'librenms'@'localhost' IDENTIFIED BY 'YOURPASSWORD'; (Hit Enter)
GRANT ALL PRIVILEGES ON librenms.* TO 'librenms'@'localhost'; (Hit Enter)
FLUSH PRIVILEGES; (Hit Enter)
exit
```
#### Open with one text editor /etc/mysql/mariadb.conf.d/50-server.cnf file:
```
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
#### Within the [mysqld] section add:
```
innodb_file_per_table=1
sql-mode=""re going to configure the web server. (Apache2)
```
#### Install and Configure Apache:
```
apt-get install libapache2-mod-php7.0 php7.0-cli php7.0-mysql php7.0-gd php7.0-snmp 
php-pear php7.0-curl snmp graphviz php7.0-mcrypt php7.0-json apache2 fping imagemagick 
whois mtr-tiny nmap python-mysqldb snmpd php-net-ipv4 php-net-ipv6 rrdtool git
```
Note that we installed php.mod 7.0 because it is one Ubuntu 16.04, The 14.05 can handle php5-version.

#### Open /etc/php/7.0/apache2/php.ini and search for 'date.timezone' (If you opened with nano can search with "Ctrl + W")
```
nano /etc/php/7.0/apache2/php.ini
Date.timezone (delete ; and set your time zone. Valid example is: Europe/Copenhagen)
date.timezone = Europe/Copenhagen
```
#### Do the same in /etc/php/7.0/cli/php.ini
```
nano /etc/php/7.0/cli/php.ini
date.timezone = Europe/Copenhagen
```
(Important to insert the timezone what your client is configured for.)

#### Execute the following commands: (Enables php7.0 module, Disable mpm_event, Considering conflict mpm_event and mpm preford)
```
a2enmod php7.0
a2dismod mpm_event
a2enmod mpm_prefork
phpenmod mcrypt
```

#### Add librenms user:

```
useradd librenms -d /opt/librenms -M -r
usermod -a -G librenms www-data
```
#### Go back to /opt and clone the repository:

```
cd /opt
git clone https://github.com/librenms/librenms.git librenms
```
#### Configure Web interface:

```
cd /opt/librenms
mkdir rrd logs
chmod 775 rrd
```

#### Open /etc/apache2/sites-available/librenms.conf:
```
nano /etc/apache2/sites-available/librenms.conf
```
#### And add the following lines:
```
<VirtualHost *:80>
  DocumentRoot /opt/librenms/html/
  ServerName  YOURSERVERNAME
  CustomLog /opt/librenms/logs/access_log combined
  ErrorLog /opt/librenms/logs/error_log
  AllowEncodedSlashes NoDecode
  <Directory "/opt/librenms/html/">
    Require all granted
    AllowOverride All
    Options FollowSymLinks MultiViews
  </Directory>
</VirtualHost>
 ```
#### Execute the following commands: (Enabling site librenms, Enabling module rewrite, Restarting apache2)
 ```
a2ensite librenms.conf
a2enmod rewrite
systemctl restart apache2
```

#### Disable the default site if this is the only site, what you are planning to host:
```
a2dissite 000-default
```

### Web Installer
##### Open: http://localhost/install.php
##### And follow the instructions.
----------------------------------------------------------------------------------------------------------------------------------------
#### Then configure snmpd:
```
cp /opt/librenms/snmpd.conf.example /etc/snmp/snmpd.conf
```
#### Open the configuration file:
```
nano /etc/snmp/snmpd.conf
```
#### And delete RANDOMSTRINGGOESHERE from the second line.
----------------------------------------------------------------------------------------------------------------------------------------
#### Then execute the following commands:
```
curl -o /usr/bin/distro https://raw.githubusercontent.com/librenms/librenms-agent/master/snmp/distro
chmod +x /usr/bin/distro
systemctl restart snmpd
```

#### Make the Cron job:

```
cp librenms.nonroot.cron /etc/cron.d/librenms
```

#### Copy logrotate config.
#### LibreNMS keeps logs in /opt/librenms/logs. To rotate out the old logs with using the provided logrotate config file:
```
cp misc/librenms.logrotate /etc/logrotate.d/librenms
```
#### Change the ownership on this directory:
```
chown -R librenms:librenms /opt/librenms
```
#### And for last Run validate.php as in the librenms directory:
```
cd /opt/librenms
./validate.php
```
If there is no FAIL the program should work without errors.
To make sure it works, and saved all changes let's restart some of the programs:
```
systemctl restart snmpd
systemctl restart apache2
systemctl restart mysql
```

## Futher reading
[Official Installation Guide for Ubuntu 16.04 Apache](http://docs.librenms.org/Installation/Installation-Ubuntu-1604-Apache/#clone-repo)

[Migrating from Observium to LibreNMS](https://vlan50.com/2015/04/17/migrating-from-observium-to-librenms/)
