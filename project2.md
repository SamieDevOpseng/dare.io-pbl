## WEB STACK IMPLEMENTATION (LEMP STACK)

### Step 1 – Installing the Nginx Web Server

Being able to display web pages to our site visitors, I employed Nginx a high-performance web server. I used the apt package manager to install this package Nginx.

I updating my server’s package index since this is the first of using **apt** for this project. I used apt install to get Nginx installed: using these commands

$ sudo apt update
$ sudo apt install nginx

Then i enter **Y** to confirm that I want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

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
