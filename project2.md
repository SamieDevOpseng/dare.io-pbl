## WEB STACK IMPLEMENTATION (LEMP STACK)

### Step 1 – Installing the Nginx Web Server

Being able to display web pages to our site visitors, I employed Nginx a high-performance web server. I used the apt package manager to install this package Nginx.

I updating my server’s package index since this is the first of using ***apt*** for this project. I used apt install to get Nginx installed: using these commands

$ sudo apt update
$ sudo apt install nginx

Then i entered ***Y*** to confirm that I want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

I ran the following command to verify that nginx was successfully installed and is running as a service in Ubuntu;

$ sudo systemctl status nginx

With the above command the Nginx turned green and running.

I opened TCP port 80 which is default port that web brousers use to access web pages in the Internet, So i can start receiving any traffic by the Web Server.

TCP port 22 is opened by default on EC2 machine to access it via SSH, so i added a rule to EC2 configuration to open inbound connection through port 80:

![inbound 80](https://user-images.githubusercontent.com/85954096/123864845-d3accd00-d8f0-11eb-8e8a-2ae3d90b9698.jpg)

I checked if the localhost:80 is locally on the Ubuntu shell, by running:

$ curl http://localhost:80 - which is DNS Name

It was pretty running perfectly.

So I tested my Nginx server if responding to requests from the Internet. Opened a web browser and pasted the IP address port :80 url, something like this;

http://54.86.187.142/:80 and the result was this below;

![proj _2](https://user-images.githubusercontent.com/85954096/123867498-0e643480-d8f4-11eb-8649-5ec6ee154ba3.jpg)


## Step 2 — Installing MySQL

Now i will be Installing a Database Management System (DBMS) to be able to store and manage data for the site in a relational database. MySQL is a popular relational database 
management system used within PHP environments, so I will be using it in this project.

"apt" will be used again to acquire and install this software by this command;

$ sudo apt install mysql-server

Typed Y and enter to confirm installation when prompted

I ran a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to my database system. So I Started the interactive script by running:

$ sudo mysql_secure_installation

This asked if you want to configure the VALIDATE PASSWORD PLUGIN.

I answered N for No because I want to be free to use any password and not be pinned down with a password validation policy.

I entered my password

Then the interaction session began:

I answered Y for yes all through the interaction session to setup my password validation component

After finishing with the interactions I tested if I would be able to log in to the MySQL console by typing:

$ sudo mysql

This connected to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. Got this output:

![MySQL](https://user-images.githubusercontent.com/85954096/124005836-2a70e000-d99f-11eb-838d-b0b7324f7f1e.jpg)

Exited the MySQL console by typing exit.

MySQL server is now installed and secured.


## Step 3 – Installing PHP

Installing PHP to process code and generate dynamic content for the web server. Nginx installed to serve my content and MySQL installed to store and manage my data.

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. So I will need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing and also need to install php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

To install these 2 packages at once, I ran:

$ sudo apt install php-fpm php-mysql

When prompted, I typed Y and press ENTER to confirm installation.

Now I have my PHP components installed. 



