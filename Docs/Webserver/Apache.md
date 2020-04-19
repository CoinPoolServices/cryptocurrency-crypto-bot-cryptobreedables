# How To Install Apache On Ubuntu 18.04
## Prerequisites

- A system running Ubuntu 18.04 LTS (Bionic Beaver)
- An internet connection
- Access to a user account with sudo privileges
Tools / Software
- A command-line utility (Use keyboard shortcut CTRL-ALT-T, or right-click the desktop and left-click Open Terminal)
- A firewall – the default UFW (Uncomplicated Firewall) in Ubuntu is fine
- The APT package manager, installed by default on Ubuntu

## How to Install Apache on Ubuntu
Before installing new software, it’s a good idea to refresh your local software package database to make sure you are accessing the latest versions. This helps cut down on the time it takes to update after installation, and it also helps prevent zero-day exploits against outdated software.

Open a terminal and type:
```
sudo apt-get update
```
Let the package manager finish updating.

### Step 1: Install Apache
To install the Apache package on Ubuntu, use the command:
```
sudo apt-get install apache2
```
The system prompts for confirmation – do so, and allow the system to complete the installation.

Terminal command to install Apache on Ubuntu.
### Step 2: Verify Apache Installation
To verify Apache was installed correctly, open a web browser and type in the address bar:

http://local.server.ip
The web browser should open a page labeled “Apache2 Ubuntu Default Page,” as in the image below:

apache2 the default welcome page on ubuntu
Note: Replace local.server.ip with the IP address of your server. If you are unsure what's the IP address, run the following terminal command:
```
hostname -I | awk '{print $1}'
```

The output will return your server's IP address.

### Step 3: Configure Your Firewall
Although the Apache installation process is complete, there is one more additional step. Configure the default UFW firewall to allow traffic on port 80.

Start by displaying available app profiles on UFW:
```
sudo ufw show app list
```

The terminal should respond by listing all available application profiles, as seen in the example below.

Available applications:
```
  Apache
  Apache Full
  Apache Secure
  OpenSSH
```
Use the following command to allow normal web traffic on port 80:
```
sudo ufw allow 'Apache'
```
Image of how Apache traffic is allowed in Ubuntu terminal.
Verify the changes by checking UFW status:
```
sudo ufw status
```
Check UFW status and verify that Apache traffic is allowed.
If you have other applications or services to allow, make sure you configure your firewall to allow traffic. For example, using the sudo ufw allow 'OpenSSH' command will enable secure, encrypted logins over the network.

Note: At this point, your Apache service on Ubuntu is up and running. If you are familiar with Apache, a next common step is setting up Apache virtual hosts.

Apache Configuration
Apache Service Controls
When managing a web server, it’s helpful to have some level of control over the service. You’ll probably find yourself reloading or restarting Apache quite frequently, as you make configuration changes and test them. However, it’s also helpful to be able to stop (and start) the Apache service as needed.

This operation uses the systemctl command, with a series of switches:

Stop Apache:
```
sudo systemctl stop apache2.service
```
Start Apache:
```
sudo systemctl start apache2.service
```
Restart Apache:
```
sudo systemctl restart apache2.service
```
Reload Apache:
```
sudo systemctl reload apache2.service
```
Apache Configuration Files, Directories and Modules
Now that you have Apache installed, there are a couple of other things you will need to be aware of to make content available online. Most of all, this means dealing with directories and configuration files.

## Directories
After installing, Apache by default creates a document root directory at /var/www/html. 

Any files that you place into this directory are available to Apache to distribute over the network. Which means, this is the place where you copy web page files you want to publish. This is also where you would want to install content management systems, such as WordPress.

## Configuration Files
As mentioned above, website content is stored in the/var/www/html/directory. You can create subdirectories within this location for each different website hosted on your server.

Apache creates log files for any errors it generates in the file /var/log/apache2/error.log.

It also creates access logs for its interactions with clients in the file /var/log/apache2/access.log.

Like many Linux-based applications, Apache functions through the use of configuration files. They are all located in the /etc/apache2/ directory.

Here’s a list of other essential directories:
```
/etc/apache2/apache2.conf – This is the main Apache configuration file and controls everything Apache does on your system. Changes here affect all the websites hosted on this machine.
/etc/apache2/ports.conf – The port configuration file. You can customize the ports Apache monitors using this file. By default, Port 80 is configured for http traffic.
/etc/apache2/sites-available – Storage for Apache virtual host files. A virtual host is a record of one of the websites hosted on the server.
/etc/apache2/sites-enabled – This directory holds websites that are ready to serve clients. The a2ensite command is used on a virtual host file in the sites-available directory to add sites to this location.
```
There are many directories and configuration files, which are detailed in the Apache Ubuntu documentation. These can be used to add modules to enhance Apache’s functionality, or to store additional configuration information.

# Modules
If you intend to work with software modules – applications that expand or enhance the functionality of Apache – you can enable them by using:
```
sudo a2enmod name_of_module
```
To disable the module:
```
sudo a2dismod name_of_module
```
# Glossary
- UFW – Uncomplicated Firewall, a software application that blocks network traffic (usually for security)
- SSH – Secure Shell, used for encrypted logins over a network
- APT – Ubuntu’s default package manager, used for installing and updating software packages
- GUI – Graphical User Interface – the “point and click” interface of the operating system



----------------------------------------------------------------------------------------------

Restart Apache 2 web server, enter:
# /etc/init.d/apache2 restart

OR
$ sudo /etc/init.d/apache2 restart

OR
$ sudo service apache2 restart

To stop Apache 2 web server, enter:
# /etc/init.d/apache2 stop

OR
$ sudo /etc/init.d/apache2 stop

OR
$ sudo service apache2 stop

To start Apache 2 web server, enter:
# /etc/init.d/apache2 start

OR
$ sudo /etc/init.d/apache2 start

OR
$ sudo service apache2 start

A note about Debian/Ubuntu Linux systemd users
Use the following systemctl command on Debian Linux version 8.x+ or Ubuntu Linux version Ubuntu 15.04+ or above:
## Start command ##
systemctl start apache2.service
## Stop command ##
systemctl stop apache2.service
## Restart command ##
systemctl restart apache2.service

CentOS/RHEL (Red Hat) Linux version 4.x/5.x/6.x or older specific commands
## Start ##
service httpd start
## Stop ##
service httpd stop
## Restart ##
service httpd restart

CentOS/RHEL (Red Hat) Linux version 7.x or newer specific commands
Most modern distro now using systemd, so you need to use the following command:
## Start command ##
systemctl start httpd.service
## Stop command ##
systemctl stop httpd.service
## Restart command ##
systemctl restart httpd.service

Generic method to start/stop/restart Apache on a Linux/Unix
The syntax is as follows (must be run as root user):
## stop it ##
apachectl -k stop
## restart it ##
apachectl -k restart
## graceful restart it ##
apachectl -k graceful
## Start it ##
apachectl -f /path/to/your/httpd.conf
apachectl -f /usr/local/apache2/conf/httpd.conf
