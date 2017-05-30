## VSRX Configuration + Add it to LibreNMS

#### The following document contains a guide about how to setup one Juniper VSRX router ( Which is running in VMware) and connect it with LibreNMS.

Our whole system is connected by VMnet 08 (NAT) where the Subnet IP is: 10.0.0.0 Subnet Mask: 255.255.255.0
Therefore we need to configure one interface of SRX like we can connect with the client later on.

#### Start the router, type username and password, then:
```
root@% cli
root> edit
root# set interfaces ge-0/0/0 unit 0 family inet address 10.0.0.201/24
root# commit
```
We added an IP address to ge-0/0/0, which is on the same subnet as the client (10.0.0.208)
For security reasons, JUNOS will automatically put the interface into the untrust in secure zones, so we can't reach it from the client.
```
    }
    zones {
        security-zone trust {
            tcp-rst;
        }
        security-zone untrust {
            screen untrust-screen;
            interfaces {
                ge-0/0/0.0 {
                    host-inbound-traffic {
                        system-services {
                            http;
                            https;
                            ssh;
                            telnet;
                            dhcp;
                        }
```
To be able to reach, we are going to "move" that interface from untrust to trust.(If we know it is secured of course)

#### Configure the interface and security zones:
```
root# delete security zones security-zone untrust interfaces ge-0/0/0.0
root# set security zones security-zone trust interfaces ge-0/0/0.0
```
#### Then allow traffic through the interface:
```
root# set security zones security-zone trust interfaces ge-0/0/0 host-inbound-traffic system-services all 
root# commit
```
#### Now we are able to ping our VSRX device from the client and add the device inside LibreNMS!
