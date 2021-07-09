## WEB STACK IMPLEMENTATION (LEMP STACK)

### Step 1 – Installing the Nginx Web Server

Being able to display web pages to our site visitors, I employed Nginx a high-performance web server. I used the apt package manager to install this package Nginx.

I opened my terminal

I updated my server’s package index since this is the first of using ***apt*** for this project. I used apt install to get Nginx installed: using these commands

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


## Step 4 — Configuring Nginx to Use PHP Processor

When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, I will use projectLEMP as an example domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at 
/var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. 
Instead of modifying /var/www/html, directory structure needed to be created within /var/www for my_domain website, leaving /var/www/html in 
place as the default directory to be serve if a client request does not match any other sites.

Create the root web directory for my_domain as follows: I ran

$ sudo mkdir /var/www/projectLEMP

Now needed to assign ownership of the directory with the $USER environment variable, which will reference your current system user:

$ sudo chown -R $USER:$USER /var/www/projectLEMP

Then I openned a new configuration file in Nginx’s sites-available directory using my preferred command-line editor. Here I'll use nano:

$ sudo nano /etc/nginx/sites-available/projectLEMP

A new blank file was created with the command-line editor used above, then Pasted the following bare-bones configuration:

```
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

Here’s what each of these directives and location blocks does:

listen — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.

root — Defines the document root where the files served by this website are stored.

index— Defines in which order Nginx will prioritize index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.

server_name — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.

location / — The first location block includes a try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.

location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.

location ~ /\.ht— The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root ,they will not be served to visitors.

Saved and closed the file. press down CTRL+X and then y and ENTER to confirm. (Using nano)

I Activated my configuration by linking to the config file from Nginx’s sites-enabled directory: I ran;

$ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/

This will tell Nginx to use the configuration next time it is reloaded. Now tested my configuration for syntax errors by typing:

$ sudo nginx -t

Then got this;

![syntex error](https://user-images.githubusercontent.com/85954096/124017523-6cece980-d9ac-11eb-92e0-e14710c06840.jpg)

I disabled default Nginx host that is currently configured to listen on port 80, I ran:

sudo unlink /etc/nginx/sites-enabled/default

Then reloaded Nginx to apply the changes, ran;

$ sudo systemctl reload nginx

My new website is now active, but the web root /var/www/projectLEMP is still empty. I Created an index.html file in that location 
so that I can test that my new server block works as expected:

sudo echo 'Hello LEMP from hostname' $(curl -s http://54.157.165.233/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://54.157.165.233/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

Opened my browser and pasted my URL using IP address:

http://54.157.165.233:80

Got this result;

![nginx](https://user-images.githubusercontent.com/85954096/124019973-53996c80-d9af-11eb-8922-2e337b90ffab.jpg)

I left this file in place as a temporary landing page for my application until I set up an index.php file to replace it. Once that is done, I will have it at the back of my mind to remove or rename the index.html file from your document root, as it would take precedence over an index.php file by default.

MyLEMP stack is now fully configured. 

## Step 5 – Testing PHP with Nginx

Now that my LAMP stack is completely installed and fully operational.

I will need to validate that Nginx can correctly handle .php files off to the PHP processor.

I created a test PHP file in my document root. Openned a new file called info.php within the document root in my text editor, I ran:

$ nano /var/www/projectLEMP/info.php

I pasted the following lines into the new file. This is valid PHP code that will return information about the server:

```php
<?php
phpinfo();
```

I openned my web browser by visiting the public IP address I’ve set up in your Nginx configuration file, followed by /info.php:

http://54.157.165.233/info.php

Got this result;

![php](https://user-images.githubusercontent.com/85954096/124030743-1b4c5b00-d9bc-11eb-9f25-613580914df5.jpg)

After checking the relevant information about the PHP server through the page, It’s best to remove the file created 
as it contains sensitive information about the PHP environment and the Ubuntu server. I used ***rm*** to remove the  file:

$ sudo rm /var/www/your_domain/info.php

This file can always be regenerated if needed later.

## Step 6 — Retrieving data from MySQL database with PHP

I be creating a test database (DB) with “To do list” and configured access to it, so the Nginx website would be able to query data from the DB and display it.

The native MySQL PHP library ***mysqlnd*** doesn’t support ***caching_sha2_authentication***, the default authentication method for MySQL 8. I'll need to create a new user with the ***mysql_native_password*** authentication method in order to be able to connect to the MySQL database from PHP.

I'll create a database named example_database and a user named samuel_user.

I connected to the MySQL console using the root account, ran:

$ sudo mysql

To create a new database, I ran the following command from the MySQL console:

mysql> CREATE DATABASE `example_database`;

I created a new user named ***samuel_user***, using ***mysql_native_password*** as default authentication method and user’s password as ***samuel***, with the following command.

mysql>  CREATE USER 'samuel_user'@'%' IDENTIFIED WITH mysql_native_password BY 'samuel';

I gave this user permission over the example_database database:

mysql> GRANT ALL ON example_database.* TO 'samuel_user'@'%';

Exit the MySQL shell with:

mysql> exit

I tested if the new user has the proper permissions by logging in to the MySQL console again, with different custom user credentials:

$ mysql -u samuel_user -p

The -p flag in this command will prompt the password used when creating the samuel_user user. After logging in to the MySQL console with the password created, I was granted access to the example_database database: following this commamd

mysql> SHOW DATABASES;

The output was this;

![showcase database](https://user-images.githubusercontent.com/85954096/124167431-9fa8e780-da69-11eb-8890-3cae79277f4d.jpg)

Now created a test table named todo_list. From the MySQL console, I ran the following statement:

CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT, content VARCHAR(255), PRIMARY KEY(item_id));

Inserting a few rows of content in the test table. I repeated the next command a few times, using different VALUES:

mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");

To confirm that the data was successfully saved to your table, run:

mysql>  SELECT * FROM example_database.todo_list;

After confirming that I have valid data in your test table, I exited the MySQL console:

mysql> exit

Now I created a PHP script that will connect to MySQL and query for your content. I Created a new PHP file in the custom web root directory using my preferred editor.

$ vi /var/www/projectLEMP/todo_list.php

The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results
in a list. If there is a problem with the database connection, it will throw an exception.

Copied this content into my todo_list.php script:

```
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```

Saved and closed, ran:

$ esc :wq! enter

I opened my web browser by visiting public IP address configured for my website, followed by /todo_list.php:

http://54.86.187.142/todo_list.php

Got this out;

![todo list](https://user-images.githubusercontent.com/85954096/124171672-79d21180-da6e-11eb-8bf6-d0cc9ccb040e.jpg)


That shows my PHP environment is ready to connected and interacting with the MySQL server.
