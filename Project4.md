## MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

### MEAN Stack is a combination of following components:

MongoDB{:target="_blank"} (Document database) – Stores and allows to retrieve data.

Express (Back-end application framework) – Makes requests to Database for Reads and Writes.

Angular (Front-end application framework) – Handles Client and Server Requests

Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user



### Step 1: Installing of NodeJs

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.

I updated ubuntu with the command: 

sudo apt update

Then Upgraded ubuntu with command:

sudo apt upgrade

I Installed NodeJS, i ran:

sudo apt install -y nodejs


### Step 2: Installing MongoDB

MongoDB stores data in flexible, Like JSON documents. Fields in a database can vary from document to document and data 
structure can be changed over time. For application, I will be adding book records to MongoDB that contain book name, 
isbn number, author, and number of pages.

I ran the command;

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

### Installed MongoDB

sudo apt install -y mongodb

I Started The server, i ran;

sudo service mongodb start

Then Verify that the service is up and running. i ran;

sudo systemctl status mongodb

![mongodb](https://user-images.githubusercontent.com/85954096/129097054-282f6da6-3b79-4a00-a25a-1f41c852256a.jpg)

After confirmed mongodb up and running perfectly

I Installed [npm] (https://www.npmjs.com) – Node package manager.

sudo apt install -y npm

### Installing ‘body-parser package

I needed ‘body-parser’ package to help us process JSON files passed in requests to the server. I ran;

sudo npm install body-parser

I Created a folder named ‘Books’ and created a file name books, I ran;

mkdir Books && cd Books

In the Books directory, I Initialize npm project with command, I ran

npm init

Added a file to it named server.js by opening the editor, I ran;

vi server.js

Copied and pasted the web server code below into the server.js file.

```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```

### Step 3: Install Express and set up routes to the server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. I used Express in the pass book information to and from our MongoDB database.

I also used Mongoose package which provides a straight-forward, schema-based solution to model your application data. I used Mongoose to establish a schema for the database to store data of our book register.

I Installed Express and Mongoose package with the command;

sudo npm install express mongoose

In ‘Books’ folder, I created  a folder named apps; I ran

mkdir apps && cd apps

I Created a file named routes.js by openning the vi editor

vi routes.js

Copied and pasted the code below into routes.js

```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```

In the ‘apps’ folder, I created a folder named models

mkdir models && cd models

I added a file named book.js

vi book.js

Copied and pasted the code below into ‘book.js’

```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```

### Step 4 – Access the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, AngularJS is used to connect to web page with Express and perform actions on book register.

I Changed the directory back to ‘Books’, I ran;

cd ../..

I Created a folder named public

mkdir public && cd public

Added a file named script.js

vi script.js

Copied and pasted the Code below (controller configuration defined) into the script.js file.

```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```
In public folder, I created a file named index.html;

vi index.html

Cpoied and pasted the code below into index.html file.

```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

Changed the directory back up to Books. I ran;

cd ..

Started the server by running this command:

node server.js

After the server is up and running, I connected  via port 3300 I tried  a separate console to test with curl command returns locally.

curl -s http://localhost:330

In this case, it shall return an HTML page, which is hardly readable in the CLI, but tried to access it from the browser.

For this – I opened TCP port 3300 in your AWS Web Console for your EC2 Instance.

I copied my IP address to my brower and added 3300 port at the back 

So I got this result below;

![Screenshot 2021-08-10 152438](https://user-images.githubusercontent.com/85954096/129106224-b1d8d914-9570-4e36-b0b1-13d1048f342a.jpg)


THANK YOU.
