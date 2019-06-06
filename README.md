# LinuxServerConfiguration
FSWD Linux Server Configuration


## Create AWS account

## Create virtual server with Lightsale

## Create new instance on Lightsale
Change AWS region to Germany, Frankfurt, Zone A
Select platform Linux/Unix
Select blueprint Ubuntu 18.04 LTS image
Create instance
Set instance identifier to Ubuntu-512MB-Frankfurt-1
Wait until the instance status changes form pending to running
Public IP-Address of the instance is 18.196.59.21
User name is ubuntu

## Enable SSH connection from the remote machine to the AWS lightsale instance
Download SSH key firl LightsailDefaultKey-eu-central-1.pem from the AWS user account
```$chmod 600 LightsailDefaultKey-eu-central-1.pem```
```$ssh -i LightsailDefaultKey-eu-central-1.pem ubuntu@18.196.59.21```

## Update the system
```$sudo apt-get update``` : update available package list
```$sud apt-get upgrade``` : update installed packages
```$sudo reboot```

## Change SSH port

Add custom port 2200 to the AWS network firewall

```$cd /etc/ssh```
```$sudo nano sshd_config```
Change #Port 22 to 2200
Save changes with ```[option^]+[x]```

sudo service restart sshd

## Setup and enable the ubuntu firewall

UFC stands for Uncomplicated firewall
```
$sudo ufw status
$sudo ufw default deny incoming
$sudo ufw default allow outgoing
$sudo ufw allow 2200
$sudo ufw allow 80
$sudo ufw allow 123
$sudo ufw enable
$sudo ufw status
```

```
ubuntu@ip-172-26-7-226:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
2200                       ALLOW       Anywhere                  
80                         ALLOW       Anywhere                  
123                        ALLOW       Anywhere                  
2200 (v6)                  ALLOW       Anywhere (v6)             
80 (v6)                    ALLOW       Anywhere (v6)             
123 (v6)                   ALLOW       Anywhere (v6)    1. 
