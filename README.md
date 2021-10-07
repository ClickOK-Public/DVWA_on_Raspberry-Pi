# SumItUp - Running DVWA on Raspberry-Pi with Ubuntu 20.04 LTS server

By: Dale P. Bada, ClickOK.no<br>
Date: 2021-10-06

## Abstract

Estimated reading time: 5 minutes

Estimated installation runtime: ca. 35 minutes (depending on hardware and internet access speed)

This article is about installing Ubuntu Server LTE 20.04.2 for Raspberry-Pi and DVWA (Damn Vurnerable Web Application) for Penetration Testing.

## Target Audience

You want to setup a local server running DVWA for Penetration testing and Cyber Security research or studies.

Perhaps you are a hobbist, or a internet and computing enthusiast who want to delve into Cyber Security.

## Requirements

**Prerequisites**
- A working network (WiFi of ethernet)
- A client PC/Laptop (to conduct the staging and do the Penetration Test from.)

**Hardware**
- Raspberry-Pi 4, model B with at least 2 GB
- MicroSD for the Raspberry-pi's to run the Server and Web Application from.
- MicroSD to USB adapter

**Software**
- Balena Etcher (or equivalent software that can create a bootable MicroSD from the Ubuntu server .iso file.)
- AppImage launcher (To run Balena Etcher, for Linux users.)
- SSH client (preinstalled on all Windows 10, Linux and Mac)

# Introduction

This article outlines a way to easily setup a pen-testing environment using Ubuntu 20.04 LST server hosting DVWA.

**Why:**
- Its fun to get hands-on installing the server and the individual components required to run DVWA from the ground up.
- Its an alternative to running the DVWA appplication on a Virtual Environment.
- DVWA server is "always on" independent on client.
- DVWA server can be accessed by muiltiple clients at the same time in the same network.

# Preparations

## Creating a MicroSD boot drive

### Linux: AppImage (to run Balena Etcher)

When working from a Linux OS environment, I prefer to run application as a "universal binary package". This eliminates unwanted system clutter that may be instroduced with normal package installation procedures. A binary package also ensures that all application dependencies are in one binary file. It eliminates invocation of sudo for package installation, making applications executable with normal user priviledges.

Below is a qquick quide for installting AppImage:

**Install on Arch based Linux**

Ubuntu runs AppImage files from the get-go, no installations required.

Online Sources and References:

| Source | Purpose | URL | 
| ------ | ------- | --- |
| Forum Post | instruction on how to in stall AppImage on Arch Linux | https://forum.manjaro.org/t/how-to-use-appimage-on-manjaro-arch-based-linux/34375 |
| Online Article - It's FOSS | Overview of appimage and how to work with it | https://itsfoss.com/use-appimage-linux/ |

**Install on Debian based Linux**

> sudo apt install appimagelauncher

**Run AppImage applications**

Running AppImage applications requires that the executable AppImage file is executable ```sudo chmod +x <appimage file name>``` then run ```./<appimage file name> ```.

**Source:**
- https://docs.appimage.org/introduction/quickstart.html#ref-how-to-run-appimage
- https://docs.appimage.org/user-guide/run-appimages.html


### Balena Etcher

Download the Balena Etcher file, which will be used to creat a bootable MicroSD drive. Make sure to download the AppImage version (64bit) of the application.

Online Sources and References:

| Source | Purpose | URL |
| ------ | ------- | --- |
| Official site - Balena | Download application | https://www.balena.io/etcher/ |


### Download Ubuntu Server 20.04

**Download**

Download from website and save file to desired location:
- Browse to: https://ubuntu.com/download/raspberry-pi/thank-you?version=20.04.3&architecture=server-arm64+raspi


As an alternative, Download with *wget* command:

Syntax: ```wget <URL + file name>```

> wget https://cdimage.ubuntu.com/releases/20.04.3/release/ubuntu-20.04.3-preinstalled-server-arm64+raspi.img.xz

**Verify hash**

To make sure the file is not corrupted or altered in any way, run a hash check on the downloaded files on Linux:

Syntax: ```echo <hash value> "file name" | shasum -a 256 -c```

> echo "31884b07837099a5e819527af66848a9f4f92c1333e3ef0693d6d77af66d6832 *ubuntu-20.04.2-preinstalled-server-arm64+raspi.img.xz" | shasum -a 256 --check

**Online Sources and References:**

| Source | Purpose | URL |
| ------ | ------- | --- |
| Official download - Ubuntu | Download application | https://ubuntu.com/download/raspberry-pi |
| Official tutorial - Ubuntu | Step-by-Step instruction on how tu install the Ubuntu Server on Raspberry-Pi | https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#1-overview |

### Create bootable MicroSD drive

**Steps:**
- Launch Balena Etcher
  - By locating the Balena Etcher AppImage file and double-clicking on it.
- "Flas from file"
  - Select the Ubuntu .iso file source file
- "Select target"
  - Select the MicroSD target destination
- "Flash!"
  - Start transferring data to MicroSD.

### Preconfigure Wifi/Network configuration

Preconfiguring WiFi network setting allows for a "headless" Ubuntu server installation.

Simply plug in the Raspberry-pi to the power, turn it on, then wait a 5 minutes or so and the new server is ready for SSH connection.

Thus, no need to connect a monitor, mouse and keyboard to manage, and play with the devise.

**WiFi (and ethernet) network configuration**

Once the bootable MicroSD is created (Balena Etcher has completed to transfer the source to the target), mount the "system-boot" partition of the newly created MicroSD boot drive.


- Locate the ```network-config``` file.
  - Edit the file with required network configuration
  - Make sure to enter the correct parameters according to your WiFi
  - The below example configures the ethernet port, if used, with IP address from the DHCP server. Otherwise, the WiFi is meant to be used and configured with a static IP address.
  ```
  # This file contains a netplan-compatible configuration which cloud-init
  # will apply on first-boot. Please refer to the cloud-init documentation and
  # the netplan reference for full details:
  #
  # https://cloudinit.readthedocs.io/
  # https://netplan.io/reference
  #
  # Some additional examples are commented out below

  version: 2
  ethernets:
    eth0:
      dhcp4: true
      optional: true
  wifis:
    wlan0:
      dhcp4: false
      optional: true
      access-points:
        "<WiFi name/ SSID>":
          password: "<WiFi password>"
      addresses: [IP address in CIDR notation]
      gateway4: <gateway IP address>
      nameservers:
        addresses: [<dns IP address>, <optional secondary dns IP address>]
  ```

Source:
- https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#1-overview
- https://alexbostock.medium.com/how-to-setup-a-headless-raspberry-pi-96b8f875d1c2


### Server configuration

**Step 1: Remote Login**

With the MicroSD card in the Raspberry-pi, simply plug in and power up the device. Wait for a few moments, give it 5 minutes or so to make some automated adjustments, then SSH to the set IP address (provided everything went well).

Syntax: ```ssh <username>@<IP address>```

Example:

> ssh ubuntu@192.168.1.100

**Note:**
Default user name and password:
User: ubuntu
Password: ubuntu

Change the password as promted in the initial login.

**Step 2: System update and upgrade**

Example:
> sudo apt update && sudo apt upgrade -y

**Step 3: Set keyboard layout**

Example:
> sudo dpkg-reconfigure keyboard-configuration

Follow the prompts and instruction on screen. Then apply the changes.

Example:
> sudo setupcon

Source: https://vitux.com/how-to-change-the-keyboard-layout-in-ubuntu/

**Step 4: Rename server**

Syntax: ```sudo hostnamectl set-hostname <new hostname>```

Example:
> sudo hostnamectl set-hostname dvwa-server

Edit a parameter in the cloud.cfg file, while first making sure to backup the original file to cloud.cfg-bak.

Example:
> sudo cp /etc/cloud/cloud.cfg{,-bak} && sudo nano /etc/cloud/cloud.cfg

- Find the paremeter ```preserve_hostname: false```
- Change the paremeter to ```preserve_hostname: true```

Source: https://www.cyberciti.biz/faq/how-to-change-hostname-on-ubuntu-20-04/

Reboot server

Example:
> sudo shutdown -r 0


## **Step 5: Install and configure DVWA

After server reboot run, the following commands:

- > sudo mkdir /var/www
- > sudo mkdir /var/www/html
- > cd /var/www/html
- > sudo git clone https://github.com/digininja/DVWA.git
- > sudo apt install -y apache2 mariadb-server php php-mysqli php-gd libapache2-mod-php
- > sudo cp /var/www/html/DVWA/config/config.inc.php.dist /var/www/html/DVWA/config/config.inc.php
- > sudo chmod 757 -R /var/www/html/DVWA/hackable/uploads/
- > sudo chmod 757 -R /var/www/html/DVWA/config/
- > sudo chmod 757 /var/www/html/DVWA/external/phpids/0.6/lib/IDS/tmp/phpids_log.txt


Configure parameters in the ```config.inc.php.dist``` file.

Example:
> sudo cp config.inc.php.dist config.inc.php && sudo nano config.inc.php

- Find line:
  - $_DVWA[ 'db_password' ] = 'p@ssw0rd';
- Change to desired password:
  - $_DVWA[ 'db_password' ] = 'pass';

Configure parameters in the ```php.ini``` file.

Example:
> sudo cp /etc/php/7.4/apache2/php.ini{,-bak} && sudo nano /etc/php/7.4/apache2/php.ini

- Find line:
  - allow_url_include = Off
- Change line to 
  - allow_url_include = On
- Restart apache2 service

Example:
> sudo systemctl restart apache2

## Create DVWA database

Note:
[] - To-do: Verify if this step is required.

Example:
> sudo mariadb

In MariaDb console, enter following commands:
- > CREATE DATABASE dvwa;
- > CREATE USER dvwa@localhost IDENTIFIED BY 'pass';
- > GRANT ALL ON dvwa.* TO dvwa@localhost;
- > FLUSG PRIVILEGES;
- > EXIT

<hr>

The setup is now complete. Access DVWA via browser using following URL:
- 192.168.20.161/DVWA

The DVWA login page will then appear.
