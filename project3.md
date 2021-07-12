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

Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the ***index.js*** application file.

I Started the server using the command below:

node index.js

When I was trying to restart the node index.js I encounted an Error: see below

![adrinuse](https://user-images.githubusercontent.com/85954096/125221761-5c7f1d80-e28e-11eb-882f-b11c48b03522.jpg)

So I had to go on google to know the cause of the error with the search word "nodejs listen eaddrinuse address already in use", I got a recent link to the solution, see below:

https://levelup.gitconnected.com/how-to-kill-server-when-seeing-eaddrinuse-address-already-in-use-16c4c4d7fe5d

### THE PROMBLE

When trying to restart a Node application, the previous one did not shut down properly, and you may see a “listen EADDRINUSE: address already in use”.

### SOLUTION

When this EADDRINUSE issue has already happened, In order to resolve it, you need to kill the process manually. In order to do 
that, you need to find the process id (PID) of the process. You know the process is occupying a particular port on your machine 
or server.

I used the command prompt to look for the process ID of the process, I ran:

lsof -i tcp:5000

Then killed the process completely with the process id number, I ran:

kill -9 3100

see result below:

![solution](https://user-images.githubusercontent.com/85954096/125223578-9ef62980-e291-11eb-8898-fad164a6f8ce.jpg)

Then successfully restart my node index.js, i used:

node index.js

Testing Backend Code without Frontend using Restful API

So the To-Do application backend part has been written and configured a database, but the frontend UI has not been configured yet. 
needed ReactJS code to achieve that. But during development, I needed a way to test the code using RESTfulL API. 
Therefore, so used some API development client to test the code which was Postman.


Tested all the API endpoints and made sure they are working. For the endpoints that require body, I sent JSON back with the necessary fields since it’s what I setup in the code.

I opened Postman, created a POST request to the API with IP address http://3.238.192.245:5000/api/todos. This request sends a new task to To-Do list so the application could store it in the database.

I made sure I set header key  to Content-Type as application/json

Created a GET request to the API on http://3.238.192.245:5000/api/todos. This request retrieves all existing records 
from out To-do application (backend requests these records from the database and sends it back as a response to GET request).

### STEP 2 – FRONTEND CREATION

After successfully done with the functionality I wanted from backend and API, I now created a user interface for a Web client (browser) to interact with the application via API. To start with the frontend of the To-do app, I used the create-react-app command to scaffold the app.

In the same root directory as the backend code, which is the Todo directory, i ran:

npx create-react-app client

This created a new folder in Todo directory called client, where I added all the react code.

#### Running a React App

Before testing the react app, there are some dependencies that need to be installed.

I Installed concurrently. It is used to run more than one command simultaneously from the same terminal window.

npm install concurrently --save-dev

I Installed nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.

npm install nodemon --save-dev

I opened the package.json file in Todo folder. Changed the scripts code to new one below: 

```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

#### Configure Proxy in package.json

I Changed directory to ‘client’

cd client

I Opend the package.json file

vi package.json

Added the key value pair in the package.json file "proxy": "http://localhost:5000".

The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly 
from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

npm run dev

In order to access the application from the Internet i opened TCP port 3000 on EC2 by adding a new Security Group rule.

#### Creating your React Components

One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For Todo app, there will be two stateful components and one stateless component.

From your Todo directory, I ran:

cd client

moved to the src directory

cd src

Insided ***src folder*** I created another folder called components

mkdir components

Moved into the components directory with

cd components

Inside ‘components’ directory i created three files Input.js, ListTodo.js and Todo.js.

touch Input.js ListTodo.js Todo.js

Opened Input.js file

vi Input.js

Copied and pasted the following:

```
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input
```

To make use of Axios, which is a Promise based HTTP client for the browser and node.js, I took some steps back using cd .. into client from 
on my terminal and ran npm install axios.

Move to the src folder

cd ..

Move to clients folder

cd ..

Install Axios

npm install axios

Go to ‘components’ directory

cd src/components

After that opened ListTodo.js

vi ListTodo.js

in the ListTodo.js copied and pasted the following code:

```
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
```

Then in Todo.js file i wrote the following code:

```
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
```

I made a little adjustment to the react code. I deleted the logo and adjusted App.js to look like this.

Move to the src folder

cd ..

in the src folder, I ran:

vi App.js

Copied and pasted the code below into it

```
import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```

After pasting, exit the editor.

In the src directory open the App.css

vi App.css

Then pasted the following code into App.css:

```
.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
```
esc :wq! to Exit

In the src directory open the index.css

vim index.css

Copied and pasted the code below:

```
body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}
```

Go to the Todo directory

cd ../..

When you are in the Todo directory run:

npm run dev

Got this result

![port 3000](https://user-images.githubusercontent.com/85954096/125230997-855bde80-e29f-11eb-9592-9f1896604f8d.jpg)

In this Project #3 I created a simple To-Do and deployed it to MERN stack. I wrote a frontend application using React.js that communicates with a backend application written using Expressjs. I created a Mongodb backend for storing tasks in a database.

### MERN PROJECT COMPLETED
