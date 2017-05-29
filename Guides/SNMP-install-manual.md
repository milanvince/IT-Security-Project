# SNMP configure and install on Ubuntu 14.04 Server 
SNMP is an universal protocol which can be used to monitor and manage networked devices.
To be able to reach our server from the client and able to monitor by LibreNMS they need a communication way.

### Install SNMPD:
```
sudo apt-get install snmpd
```

### Create a backup file of snmpd.conf original file what we can edit later:
```
sudo nano /etc/snmp/snmpd.conf /etc/snmp/snmpd.conf.ori
```

### Create the new snmpd.conf file:
```
sudo nano /etc/snmp/snmpd.conf
```

### Add the following lines to the file:
```
rocommunity public
syslocation "Your Location"
syscontact admin@domain.com
```

### Edit the /etc/default/snmpd file:
```
sudo nano /etc/default/snmpd
```

### Delete the following line:
```
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -g snmp -I -smux -p /var/run/snmpd.pid'
```
### And add this:
```
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid -c /etc/snmp/snmpd.conf'
```

### Lastly restart SNMP:
```
sudo /etc/init.d/snmpd restart
* Restarting network management services
```

### Now we would be able to monitor our server by Librenms. Just open it from the client, and select add device.
