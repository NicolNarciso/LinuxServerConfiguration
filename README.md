# LinuxServerConfiguration
FSWD Linux Server Configuration


## Setup the vitural server on AWS

### 1. Create AWS account
### 2. Create virtual server with Lightsale
### 3. Create new instance on Lightsale
* Change AWS region to Germany, Frankfurt, Zone A.
* Select platform Linux/Unix.
* Select blueprint Ubuntu 18.04 LTS image.
* Create instance.
* Set instance identifier to Ubuntu-512MB-Frankfurt-1.
* Wait until the instance status changes form pending to running.

## My virtual server
* Public IP-Address of the server instance: `18.196.59.21`.
* Initial user name: `ubuntu`.

## Setup the security
### 1. Enforce key-based SSH authentication
Enable SSH connection from the local machine to the AWS lightsale instance with RSA key authentication
* Download RSA SSH key file `LightsailDefaultKey-eu-central-1.pem` from the AWS user account website.
* Optional: Restrict the read and write permission for the key file: `$ chmod 600 LightsailDefaultKey-eu-central-1.pem`.
* Open SSH from the local machine.
  * Open new terminal from the folder that contains the downloaded key file.
  * Open SSH connection to the virtual server usind the key-based authentication: `$ ssh -i LightsailDefaultKey-eu-central-1.pem ubuntu@18.196.59.21`.

### 2. Update the system
All system packages needs to be updated to most recent versions.
* Update the available package list: `$ sudo apt-get update`.
* Update all installed packages: `$ sud apt-get upgrade`.
* Reboot the server: `$ sudo reboot`

### 3. Change SSH port to a non-default port
1. Add custom port 2200 to the AWS network firewall on the AWS instance network configuration website.
2. Change the SSH server port on the virtural server from 22 to 2200
  * Open SSH connection
  * Navigate to the SSH config folder: `$ cd /etc/ssh`
  * Open the SSH config file with text editor nano: `$ sudo nano sshd_config`
  * Change line `#Port 22` to `#Port 2200`
  * Save file: `[option^]+[X]`
  * Restart SSH service: `$ sudo service restart sshd`

### 4. Configure the ubuntu firewall
The UFC firewall shall only allow SSH, HTTP, and NTP. 
>_UFC stands for Uncomplicated firewall_

* Check the current status of the firewall: `$ sudo ufw status`.
* Disable all ports for incoming requests: `$ sudo ufw default deny incoming`.
* Enable all ports for outgoing communication:`$ sudo ufw default allow outgoing`.
* Enable SSH port: `$ sudo ufw allow 2200`.
* Enable HTTP port: `$ sudo ufw allow 80`.
* Enable NTP port: `$ sudo ufw allow 123`.
* Enable the firewall: `$ sudo ufw enable`.
* Check the firewall configuration: `$ sudo ufw status`.

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
  ```


## Setup the user management

### 1. Create new user

### 2. Create key-based login for the new user

### 3. Give the new user sudo access

### 4. Disable remote root login
