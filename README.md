# Linux-Server-Configuration-UDACITY

## Server details
IP address: 35.229.70.136

SSH port: `2200`

URL: http://35.229.70.136/

login to the server by

```
ssh grader@35.229.70.136  -p 2200        

```
# Configurations

## Add user
Add user grader : `sudo useradd grader`

## Add user grader to sudo group
`usermod -aG sudo grader`

## Update all currently installed packages

`apt-get update`

`apt-get upgrade`

Then restarted the machine.

### Generate a Key Pair
From the local side using
`ssh-keygen` - to generate the keys

In the grader sever
```
mkdir ~/.ssh
chmod 700 ~/.ssh
```
```
nano ~/.ssh/authorized_keys
```
Adding the public key by coping it from the local Machine
```
chmod 600 ~/.ssh/authorized_keys`
exit
```
Public key location on the server `~/.ssh/authorized_keys`
### Disable Password Authentication

by modfiying the ssh_config using `sudo nano /etc/ssh/sshd_config`
and making `PasswordAuthentication no`
then reloading the system by `sudo systemctl reload sshd`

## Change SSH port from 22 to 2200
Edit the file `/etc/ssh/sshd_config` and change the line Port 22 to: Port 2200

### Time Zone Configuration 
```
sudo dpkg-reconfigure tzdata
```

### Fire wall settings
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections
for SSH (port 2200), HTTP (port 80), and NTP (port 123).
	
Block all incoming connections as in lectures:

`sudo ufw default deny incoming`

Allow outgoing connection on all ports:

`sudo ufw default allow outgoing`

Allow incoming connection for SSH on port 2200:

`sudo ufw allow 2200/tcp`

Allow incoming connections for HTTP on port 80:

`sudo ufw allow www`

Allow incoming connection for NTP on port 123:

`sudo ufw allow ntp`
`sudo ufw enable`

### Database user creation 
Using command 
```
sudo -u postgres psql
create user catalog with password 'password';
create database catalog with owner catalog;
```
### Clone and setup the Item Catalog

Clone repository : `https://github.com/MonicaSalama/fullstack-nanodegree-vm.git`

Then I have added the line ` WSGIScriptAlias / /var/www/fullstack-nanodegree-vm/vagrant/catalog/project.wsgi` to
the file `/etc/apache2/sites-enabled/000-default.conf` to tell apache to run the project.wsgi file as default

Then I made the wsgi file in the directory `/var/www/fullstack-nanodegree-vm/vagrant/catalog`
and adding this code to it to run `project.py`

```
import sys
import os
import logging
logging.basicConfig(stream=sys.stderr)
sys.stdout = sys.stderr
sys.path.insert(0,"/var/www/fullstack-nanodegree-vm/vagrant/catalog/")
os.chdir("/var/www/fullstack-nanodegree-vm/vagrant/catalog/")
from project import app as application
application.secret_key = 'super_secret_key'
```

## Tools installed

Apache and WSGI by command : `sudo apt-get install apache2 libapache2-mod-wsgi-py3`

postgresql by command : `sudo apt-get install postgresql`

git : `sudo apt-get install git`

## Dependencies for the catalog project to run on the server

```
sudo apt-get -qqy install make zip unzip postgresql
sudo apt-get -qqy install python3 python3-pip
sudo -H pip3 install --upgrade pip
sudo -H pip3 install flask packaging oauth2client redis passlib flask-httpauth
sudo -H pip3 install sqlalchemy flask-sqlalchemy psycopg2 bleach requests
```
## Note
I wasn't able to create Amazon account for now, so I used a google cloud instance from one of my friends for creating the grader server and configuring it.
