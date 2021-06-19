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

