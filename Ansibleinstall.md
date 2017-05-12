# How to install Ansible on Ubuntu 14.04

During our project we will use ansible at most parts. In this document is a short guide how to install on your Linux client.

## Step 1
Let's update the system and manage the repositories that you will install software from.
```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
```
## Step 2
Add the ansible repository to your system
```
$ sudo apt-add-repository ppa:ansible/ansible
```
## Step 3
Let's update again and install Ansible
```
$ sudo apt-get update
$ sudo apt-get install ansible
```
That's it! Now we got installed Ansible on our machine. :)

## Futher reading
Package: software-properties-common (0.96.20.2-1) 
