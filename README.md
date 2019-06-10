# LinuxServerConfiguration
FSWD Linux Server Configuration


## Setup the vitural server on AWS

### 1. Create AWS account
### 2. Create new virtual server with Lightsale
* Change AWS region to Germany, Frankfurt, Zone A.
* Select platform Linux/Unix.
* Select blueprint Ubuntu 18.04 LTS image.
* Create instance.
* Set instance identifier to Ubuntu-512MB-Frankfurt-1.
* Wait until the instance status changes form pending to running.

## 3. My virtual server
* Public IP-Address of the server instance: `3.121.207.106`.
* Initial user name: `ubuntu`.

## Setup the security
### 1. Enforce key-based SSH authentication
Enable SSH connection from the local machine to the AWS lightsale instance with RSA key authentication
1. Download RSA SSH key file `LightsailDefaultKey-eu-central-1.pem` from the AWS user account website.
2. Restrict the read and write permission for the key file: `$ chmod 600 LightsailDefaultKey-eu-central-1.pem`. It is required that your private key files are NOT accessible by others.
3. Open SSH from the local machine.
  * Open new terminal from the folder that contains the downloaded key file.
  * Open SSH connection to the virtual server usind the key-based authentication: `$ ssh -i LightsailDefaultKey-eu-central-1.pem ubuntu@3.121.207.106`.

### 2. Update the system
All system packages needs to be updated to most recent versions.
* Update the available package list: `$ sudo apt-get update`.
* Update all installed packages: `$ sud apt-get upgrade`.
* Reboot the server: `$ sudo reboot`

### 3. Change SSH port to a non-default port
1. Configure the AWS network firewall on the AWS instance network configuration website.
 * Add custom port 2200 for SSH
 * Add custom port 123 for NTP
 * Remove default SSH port 22
2. Change the SSH server port on the virtural server from 22 to 2200
  * Open SSH connection
  * Navigate to the SSH config folder: `$ cd /etc/ssh`
  * Open the SSH config file with text editor nano: `$ sudo nano sshd_config`
  * Change line `#Port 22` to `Port 2200` (Remove #)
  * Save file: `[option^]+[X]`
  * Restart SSH service: `$ sudo service sshd restart`
  
### 4. Test the configuration
1. Open SSH connection on port 2200: `$ ssh -i LightsailDefaultKey-eu-central-1.pem ubuntu@3.121.207.106 -p 2200`

### 5. Configure the ubuntu firewall
The UFC firewall shall only allow SSH, HTTP, and NTP. 
_UFC stands for Uncomplicated firewall_

* Check the current status of the firewall: `$ sudo ufw status`.
* Disable all ports for incoming requests: `$ sudo ufw default deny incoming`.
* Enable all ports for outgoing communication:`$ sudo ufw default allow outgoing`.
* Enable SSH port: `$ sudo ufw allow 2200`.
* Enable HTTP port: `$ sudo ufw allow 80`.
* Enable NTP port: `$ sudo ufw allow 123`.
* Enable the firewall: `$ sudo ufw enable`.
* Check the firewall configuration: `$ sudo ufw status`.
  ```
  ubuntu@ip-172-26-10-105:~$ sudo ufw status
  Status: active
 
  To                         Action      From
  --                         ------      ----
  2200                       ALLOW       Anywhere                  
  80                         ALLOW       Anywhere                  
  123                        ALLOW       Anywhere                  
  2200 (v6)                  ALLOW       Anywhere (v6)             
  80 (v6)                    ALLOW       Anywhere (v6)             
  123 (v6)                   ALLOW       Anywhere (v6)
  ```

### 6. Test the configuration
1. Open SSH connection on port 2200: `$ ssh -i LightsailDefaultKey-eu-central-1.pem ubuntu@3.121.207.106 -p 2200`

## Setup the user management

### 1. Create new user
* Create new unix user names "grader": `$ sudo adduser grader`.
 ```
 ubuntu@ip-172-26-10-105:~$ sudo adduser grader
 Adding user `grader' ...
 Adding new group `grader' (1001) ...
 Adding new user `grader' (1001) with group `grader' ...
 Creating home directory `/home/grader' ...
 Copying files from `/etc/skel' ...
 Enter new UNIX password: 
 Retype new UNIX password: 
 No password supplied
 Enter new UNIX password: 
 Retype new UNIX password: 
 No password supplied
 Enter new UNIX password: 
 Retype new UNIX password: 
 passwd: password updated successfully
 Changing the user information for grader
 Enter the new value, or press ENTER for the default
  Full Name []: 
  Room Number []: 
  Work Phone []: 
  Home Phone []: 
  Other []: 
 Is the information correct? [Y/n] y
 ```
 ### 3. Give the new user sudo access
* Give user "grader" sudo access: `$ sudo usermod -aG sudo grader`.

### 4. Test sudo
* Change to user "grader": `$ su grader`.
 ```
 ubuntu@ip-172-26-10-105:~$ su grader
 Password: 
 grader@ip-172-26-10-105:/home/ubuntu$
 ```
* Check sudo permission:`$ sudo -i`.
 ```
 grader@ip-172-26-10-105:/home/ubuntu$ sudo -i
 [sudo] password for grader: 
 root@ip-172-26-10-105:~# 
 ```
### 2. Create key-based login for the new user
* Login as the new user: `$ su grader`.
* Open the new users home folder: `$ cd /home/grader`.
* Check if the folder ".ssh" exists: `$ la`.
* If not, create new folder ".ssh": `$ mkdir .ssh`.
* Check if the folder was created: `$ la`.
 ```
 grader@ip-172-26-10-105:~$ la
 .bash_history  .bash_logout  .bashrc  .profile  .ssh  .sudo_as_admin_successful
 ```
* Open the .ssh folder: `$ cd .ssh`.
* Create new file "authorized_keys": `$ touch authorized_keys`.
* Setup folder permission: `$ sudo chmod 0700 /home/grader/.ssh`.
* Setup file permission: `$ sudo chmod 0644 /home/grader/.ssh/authorized_keys`
* Create new SSH keys: `$ ssh-keygen -t rsa -b 4096
 ```
 grader@ip-172-26-10-105:~/.ssh$ ssh-keygen -t rsa -b 4096
 Generating public/private rsa key pair.
 Enter file in which to save the key (/home/grader/.ssh/id_rsa): newkey  
 Enter passphrase (empty for no passphrase): 
 Enter same passphrase again: 
 Your identification has been saved in newkey.
 Your public key has been saved in newkey.pub.
 The key fingerprint is:
 SHA256:e/8cpTylUz5QJGxKgnvF8aRw47CafC500j5B9KFI4nY grader@ip-172-26-10-105
 The key's randomart image is:
 +---[RSA 4096]----+
 |     . ..+.=oo . |
 |    . o.o.O+*oo  |
 |     o E.+++o. . |
 |    . o.=. .  .  |
 |       *S=   .  +|
 |      . *..  ..B |
 |       ..+.   B..|
 |        .... . +.|
 |            ..o  |
 +----[SHA256]-----+
 ```
* Copy the public key from the generated file "newkey.pub": `$ sudo vim newkey.pub`.
 ```
 ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDPLnNq10evj38k1WtHvKzaUtZYL+vfQgqkMtVNbLO+W34EpsDwaWMj7KuZnY5FXIMijh2Gb+vAFrhk5JIZPDcJ/1OBuF8MmcWbvLfUu0jJClydtLXd+JjMDqA3zGX2gQAnPmd7K5St7OsieIgKrE1hOgiyubLfIimu/gSaWZnBVJ14/3OfIMUsQ7t2ja+scUZnt4eBF1zHbp1up7sTXhAWdN+BXbHtc3rlmywb70se5h1PJmpROvwDqRlO1EGBGzgwbTkH7aJU9h19uyd4S0lvFTpewO1SRkw6Xy3Vjd92WCr8A9wfnlU9YCyf2i/+pWqH69DudtPf+WS9KvMP3G7U6aEvB7Iq0+Rv2AF9NFpEdIbYR2NociCuUMxECVN6AqQ1aWovhqyDkBYukIcr/cr2ukTBKx9wjbrw+ifjpmjt9MI87wfhdMbQmgvcsoXCfmcktZYLvuSurhG7JzvWz0R1ANuemfb7BwcLw95vZ3OCGc8VKG0DEdEkb4t41ClIRm4wy+7Q94On0Sx8uKByQmeJRBJIv8I/smwJJKFe90LPO86c0OjoIxKcLtMWItq0Rss+DIS4nDDdijeSKv79J+Vjug2DJae8YDngtm++u7HVJA+KOZikscSVa1744SLWUrx+bvU6cTNv5QBcCuxFyUSuOrtk0uM7GVacD87fyXLXVw== grader@ip-172-26-10-105
 ```
* Paste the copied key into the file "authorized_keys": $ sudo vim authorized_keys
* Save the file "authorized_keys": `Esc` and then `:`
### 4. Disable remote root login
