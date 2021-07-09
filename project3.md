## SIMPLE TO-DO APPLICATION ON MERN WEB STACK

In this project, i will be implementing web solution based on MERN stack in AWS Cloud.

#### MERN Web stack consists of following components:

MongoDB: A document-based, No-SQL database used to store application data in a form of documents.

ExpressJS: A server side Web Application framework for Node.js.

ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.

Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

I opened my AWS account and created an EC2 instance, added Tag Project3_MERN

### STEP 1 – BACKEND CONFIGURATION

Opened my terminal 

Updated and Upgraded ubuntu with following commands, ran:

sudo apt update && sudo apt upgrade

To get the location of Node.js software from Ubuntu repositories. ran:

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

Now Install Node.js on the server, i ran:

sudo apt-get install -y nodejs

The command above gave the opportunity to installs both **nodejs** and **npm**. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

To verify the node installation with the command below

node -v 

To verify the node installation with the command below

npm -v 

Application Code Setup
To create a new directory for my To-Do project, ran:

mkdir Todo

I ran the command ***ls*** to verify that the Todo directory was created.

I changed my current directory to the newly created one, ran:

cd Todo

Now, I will be using the command ***npm init*** to initialise this project, so a new file named package.json will be created. 
This file will normally contain information about the application and the dependencies that it needs to run. I Followed the prompts 
after running the command. Then press Enter several times to accept default values, then accept to write out the package.json 
file by typing yes.

I ran the command ls to verify package.json file is created.

### INSTALL EXPRESSJS

Express is a framework for Node.js, a lot of things developers would have programmed is already taken care of out of 
the box. Therefore it development easier, and abstracts a lot of low level details. For example, Express helps to define routes 
of your application based on HTTP methods and URLs.

Now needed to install express, using npm:

npm install express

Creating a index.js file with the command below

touch index.js

Ran ls to verify that your index.js file was successfully created

Installing the dotenv module, ran:

npm install dotenv

Opened the index.js file with the command below

vim index.js

Copied and pasted the code below into it and saved.

```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

Port 5000 was specified in the code to be used. This will be accessable later when going on the browser.

Used :wq! to save and exit

I start the server to see if it works. by running command below:

node index.js

The result was successful: as shown below

![port 5000](https://user-images.githubusercontent.com/85954096/125127461-2bea8880-e0c2-11eb-8a0d-6b8a51342c28.jpg)

Now went back to my instance to open the EC2 Security Groups to create an inbound rule to open TCP port 5000.

Opened my browser and tried to access the server’s Public IP followed by port 5000, publicDNS name could also used to access the port 5000.

http://3.238.183.191:5000

The result was this:

![server running](https://user-images.githubusercontent.com/85954096/125128953-5f2e1700-e0c4-11eb-8294-ad54fa5ec90d.jpg)


