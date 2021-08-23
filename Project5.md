## CLIENT-SERVER ARCHITECTURE USING MYSQL RATIONAL DATABASE MANAGEMENT SYSTEM

### Understanding Client-Server Architecture

Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another.

In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server".

A simple diagram of Web Client-Server architecture below:

![Screenshot 2021-08-23 115918](https://user-images.githubusercontent.com/85954096/130487465-fc04a62a-93d9-4857-b123-9168f6e564f3.jpg)

In the example above, a machine that is trying to access a Web site using Web browser or simply ‘curl’ command is a client and it sends HTTP requests to a Web server (Apache, Nginx, IIS or any other) over the Internet.

If we extend this concept further and add a Database Server to our architecture, we get this picture below:

![Screenshot 2021-08-23 115959](https://user-images.githubusercontent.com/85954096/130487761-b2866cdc-dbee-4543-b1cc-b35b45385f49.jpg)

In this case, our Web Server has a role of a "Client" that connects and reads/writes to/from a Database (DB) Server (MySQL, MongoDB, Oracle, SQL Server or any other), and the communication between them happens over a Local Network (it can also be Internet connection, but it is a common practice to place Web Server and DB Server close to each other in local network).

## IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)

To demonstrate a basic client-server using MySQL Relational Database Management System (RDBMS).

Firstly, i created and configured two Linux-based virtual servers on EC2 instances in AWS.

*Named Server A: DB Server*

*Named Server B: Client Server*

On the Linux server I opened two (2) tabs for the 2 servers

Updated the two servers by running the command;   

*sudo apt update -y*

On the first Linux server I installed MYSQL Server by running the command;


*sudo apt install mysql-server -y*

Then Enabled the server by running command;

*sudo systemctl enable mysql* 

On the Second server I installed MYSQL Client by running the command;

*sudo apt install mysql-client -y*

Before configuring MySQL server to allow connections from remote hosts, I needed to run the security script since i just have the mysql installed and running no database, no user and the table. So, running the security scipt on mysql server I ran;

*sudo mysql_secure_installation*

After running the command above I was taking to VALIDATE PASSWORD COMPONENT, after setting the password I moved to the interactive stage which Y/yes was answered all through.

After setting the password and completing the interactive session I ran command below to create the user;

*sudo mysql*

*mysql: CREATE USER 'remote_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';*

*mysql: CREATE DATABASE test_db;*

*mysql: GRANT ALL ON test_db.* TO 'remote_user'@'%' WITH GRANT OPTION;*

*mysql: FLUSH PRIVILEGES;*

*mysql: exit*

Configuring MySQL server to allow connections from remote hosts, Openning vi text edited and replace the bind address ‘127.0.0.1’ to ‘0.0.0.0’; I ran;

sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf

To save and exit, I ran;

esc :wqa!

![replace](https://user-images.githubusercontent.com/85954096/130512635-342d80c9-58f3-40f6-95d0-20e65d7b77ae.jpg)



