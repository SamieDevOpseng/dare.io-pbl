## WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are; **LAMP**, **LEMP**, **MERN** and **MEAN**.

In this project i will be focusing on LAMP

L means Linus server such as windows server etc.

A means Apaches being able to access our server over internet.

M means MySql Database Service is a fully managed database service to deploy cloud-native appilcation.

P means PHP or PYTHON or PERL  is a general-purpose scripting language especially suited to web development.

I signned into my intance using my AWS account and access my terminal through MobaXterm server.


### Step 1 — Installing Apache and Updating the Firewall

To update a list of packages in package manager

I used command prompt $ sudo apt update

To run apache2 package installation

I used command prompt $ sudo apt intall apache2


**To verify that apache2 is running as a Service in our OS, I used the following command**

$ sudo systemctl status apache2

we need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet. As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:

Our server can now be access locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

To test that my Apache HTTP server is responding on the Internet. I opened a web browser of my choice and copied the following url 52.91.78.155 on the wed browser

![apache2](https://user-images.githubusercontent.com/85954096/122648704-f15b8480-d0ef-11eb-82c0-97b4dbc7e80c.png)


## Step 2 — Installing MySQL

After my web server is up and running, I install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

To acquire and install this software, I used the following command;

$ sudo apt install mysql-server

After prompted i confrimed intallation by clicking Y {yes}

It’s recommended that I run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. I Started the interactive script by running command $ sudo mysql_secure_installation

This installation asked if I want to configure the VALIDATE PASSWORD PLUGIN which I responded yes.

I implemented level of password validation by selecting 1 for medium length so i can use numeric, mixed case, and special characters.

After enabling password validation, i was shown the password strength for the root password i just entered and my server asked if i want to continue and happy with the current with that password. I enter Y for “yes” at the prompt.

Estimated strength of the password: 100 

For the rest of the questions, I press Y and hit the ENTER key at each prompt. This remove some anonymous users and the test database, disable remote root logins, 
and load these new rules so that MySQL immediately respects the changes i have made.

I ran test if i am able to log in to the MySQL console by using command $ sudo mysql.

It was successfully connected to the MySQL server as the administrative database user root without using password because the default authentication method for the administrative MySQL user is unix_socket instead of password.

Exit the MySQL console by typing exit.


## Step 3 — Installing PHP

PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, we'd need php-mysql, 
a PHP module that allows PHP to communicate with MySQL-based databases. We'd also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP 
packages will automatically be installed as dependencies.

To install these 3 packages at once, i ran:

sudo apt install php libapache2-mod-php php-mysql

Once the 3 packages finished installing i ran the following command to confirm the PHP version:

php -v

![test1](https://user-images.githubusercontent.com/85954096/122655364-65f5e980-d117-11eb-86c3-87d5f47fa6ea.jpg)

My LAMP stack is completely installed and fully operational.


## Step 4 — Creating a Virtual Host for your Website using Apache

I will set up a domain called projectlamp since is the project i am working on.

Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html 
directory. I will leave this configuration as it is and add my own directory next to the default one.

Creating a directory for projectlamp using ‘mkdir’ command as follows:
  sudo mkdir /var/www/projectlamp

I will ne assigning ownership of the directory with the $USER environment variable, which will reference my current system user by using:
  sudo chown -R $USER:$USER /var/www/projectlamp

After all that is done I created and open a new configuration file in Apache’s sites-available directory using my preferred command-line editor. 
using vi, I also use vim because they both the same:
  sudo vi /etc/apache2/sites-available/projectlamp.conf

This will create a new blank file that we allow me to paste the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>


Then saved and closed file with the following: esc :wq hit enter on the keyboard

To show the new file in the sites-available directory i used the following command "ls"

sudo ls /etc/apache2/sites-available
You will see something like this
000-default.conf  default-ssl.conf  projectlamp.conf

With this VirtualHost configuration, I telling Apache to serve projectlamp using /var/www/projectlampl as its web root 
directory. In a case i want to test Apache without a domain name, all i need do is to remove or comment out the options ServerName and ServerAlias 
by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.

I then used a2ensite command to enable the new virtual host:

sudo a2ensite projectlamp

I might choose to disable the default website that comes installed with Apache. This is required if I am not using a custom 
domain name, because in this case Apache’s default configuration would overwrite my virtual host. To disable Apache’s default website I used a2dissite command, type:

sudo a2dissite 000-default


To make sure my configuration file doesn’t contain syntax errors, i ran:

sudo apache2ctl configtest

Then reload Apache to allow changes take effect. i used:

sudo systemctl reload apache2


Now that my new website is active and running, but the web root /var/www/projectlamp is still empty. I Created an index.html file in that location so that I can test the virtual host works as expected:

sudo echo 'Hello LAMP from hostname'

I went to my browser and opened the website URL and pasted the IP address and also used the public DNS name on my browser then got this result:

![1122](https://user-images.githubusercontent.com/85954096/123133492-6642fd00-d415-11eb-8589-0304ac22e105.jpg)

## Step 5 — Enable PHP on the website

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. 
This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative 
message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance 
is over, the index.html is renamed or removed from the document root, bringing back the regular application page.

In other to execute this behavior, I edited the /etc/apache2/mods-enabled/dir.conf file and changed the order in which the index.php file is listed within 
the DirectoryIndex directive using this command:

sudo vim /etc/apache2/mods-enabled/dir.conf

I was able to make changes to this by bring the index.php forward:
<IfModule mod_dir.c>
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

After saving and closing the file, I reloaded Apache so the changes can take effect:

$ sudo systemctl reload apache2


Now I have a custom location to host your website’s files and folders, I need to create a PHP test script to confirm that Apache is able to handle and process requests for PHP files.

I Created a new file named index.php inside the custom web root folder: using vim, remembered I used vi previously creating file, we can anyone that comes to our mind.

$ vim /var/www/projectlamp/index.php


This opened a blank file. Added the following text, which is valid PHP code, inside the file:

<?php
phpinfo();

saved and closed the file, refreshed the public IP address I pasted on the browser then got this reuslt:  

![php file](https://user-images.githubusercontent.com/85954096/123139155-84136080-d41b-11eb-8f31-c6bce6d19aec.jpg)

This page provides information about our server from the perspective of PHP. It is useful for debugging and to ensure that my settings are being applied correctly.

It’s best to remove the file created as it contains sensitive information about PHP environment and our Ubuntu server. used rm to do so:

$ sudo rm /var/www/projectlamp/index.php

This page can always be recreated if needed to access the information again later.


