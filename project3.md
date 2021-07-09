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

### Route

There are three (3) actions that the To-Do application needs to be able to do:

Create a new task
Display list of all tasks
Delete a completed task

Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

For each task, I created routes that defined various endpoints that the To-do app will depend on. I used the command below to create a route folder.

mkdir routes

Changed directory to routes folder.

cd routes

I created a file api.js with the command below

touch api.js

Open the file with the command below

vim api.js

Copied and pasted below code in the file.

```
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```
saved and exit: esc :wq! enter

### MODEL

The app is going to make use of Mongodb which is a NoSQL database, Model needs to created.

A model is at the heart of JavaScript based applications, and it is what makes it interactive.

I used models to define the database schema which is important so that I will be able to define the fields stored in 
each Mongodb document. 

Schema is a blueprint of how the database will be constructed, including other data fields that may not be 
required to be stored in the database. These are known as virtual properties

To create a Schema and a model, I Changed directory back Todo folder with ***cd ..*** and installed Mongoose which is 
a Node.js package that makes working with mongodb easier. ran command:

npm install mongoose

I Created a new folder with mkdir models command

Changed directory into the newly created ‘models’ folder with cd models.

Inside the models folder, I created a file and name it todo.js. See sample below:

mkdir models && cd models && touch todo.js

Opened the file created with vim todo.js then pasted the code below in the file:

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```
saved and exit: esc :wq! enter

Now i needed to update the routes from the file api.js in ‘routes’ directory to make use of the new model.

In Routes directory, I opened api.js with vim api.js, deleted the code inside with ***:%d*** command and paste the code below into it then saved and exit

```
const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
```

### MONGODB DATABASE

I will be needing a database where to store my data. For this I will make use of mLab. mLab provides MongoDB database as a service 
solution (DBaaS), so to make life easy, I signed up for a shared clusters free account, which is ideal for my use 
case. Followed the sign up process, selected AWS as the cloud provider, and chose a region near me.

In the index.js file, Its specified process.env to access environment variables, but this file has not been created. firstly, i
Created a file in the Todo directory and name it .env. with the command below:

touch .env && vi .env

Added the connection string to access the database in it, shown below:

DB = 'mongodb+srv://mookayDB:mookay1983@cluster0.0w57i.mongodb.net/samieDB?retryWrites=true&w=majority

Now needed to update the index.js to reflect the use of .env so that Node.js can connect to the database.

Opened the file with vim index.js

Deleted by Pressing esc Typed :%d and enter

Updating by Pressing i to insert mode in vim index.js

pasted the entire code below in the file.

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

