# Introduction

MEAN Stack is a JavaScript Stack that is used for easier and faster deployment of full-stack web applications. MEAN Stack comprises 4 technologies namely: MongoDB, Express.js, Angular.js, Node.js. It is designed to make the development process smoother and easier. It is one of the most demanded tech stack between developers for creating full fledge web applications. Although it is a Stack of different technologies, all of these are based on JavaScript language.

__MongoDB:__ Non-relational open-source document-oriented database.

__Express JS:__ Node.js framework that makes it easier to organize your application’s functionality with middleware and routing and simplify APIs.

__Angular JS:__ It is a JavaScript open-source front-end structural framework that is mainly used to develop single-page web applications(SPAs).

__Node JS:__ is an open-source and cross-platform runtime environment for building highly scalable server-side applications using JavaScript.

## Step 0: Prerequisites

__1.__ EC2 Instance of t3.small type and Ubuntu 24.04 LTS (HVM) was lunched in the eu-north-1 region using the AWS console.

<img width="900" alt="createinstance" src="https://github.com/user-attachments/assets/76d9caf9-bf4c-41b3-9ef4-b498ec6c43a1" />

<img width="900" alt="instance" src="https://github.com/user-attachments/assets/b063228e-1692-472e-ba37-8bc4187e07c4" />

__2.__ Added SSH key pair named test to access the instance on port 22

__3.__ The security group was configured with the following inbound rules:

- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.

- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.

- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.

- Allow traffic on port 3300 (Custom TCP) with source from anywhere.
  
<img width="900" alt="rules" src="https://github.com/user-attachments/assets/808fc2b1-dca1-4bad-84f7-5f1cde6de337" />

__4.__ Connected using web console in Aws

<img width="900" alt="console" src="https://github.com/user-attachments/assets/fdd462b6-4771-4403-a50c-c378b4240bfb" />

## Step 1 - Install Nodejs

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.

__1.__ __Update and Upgrade ubuntu__

```
sudo apt update && sudo apt upgrade -y
```
<img width="900" alt="uu" src="https://github.com/user-attachments/assets/a0e06abe-8e3e-48db-b503-6fb6f8fb09f7" />

__2.__ __Add certificates__

```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
```
<img width="900" alt="dirmngr" src="https://github.com/user-attachments/assets/22486e54-230c-45a2-b014-d854adeeef93" />

```
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -

```
<img width="900" alt="nodecheck" src="https://github.com/user-attachments/assets/453cd8b3-ec3a-4d8b-9c35-e84f3b3c57f0" />


__.3__ __Install NodeJS__

```
sudo apt-get install -y nodejs
```
<img width="900" alt="installnodejs" src="https://github.com/user-attachments/assets/520b41c9-5bc2-4036-8567-8fe1d6a74f8b" />

## Step 2 - Install MongoDB

For this application, Book records were added to MongoDB that contain book name, isbn number, author, and number of pages.

__1.__ __Download the MongoDB public GPG key__

```
sudo apt-get install -y gnupg curl

curl -fsSL https://pgp.mongodb.com/server-8.0.asc | \
  sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg \
  --dearmor
```
<img width="900" alt="installmongo" src="https://github.com/user-attachments/assets/7f712d46-4a93-4b77-9bb6-0d0f05defd92" />

__2.__ __Add the MongoDB repository__

```
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/8.0 multiverse" | \
sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
```
<img width="900" alt="deb" src="https://github.com/user-attachments/assets/36ecaf9b-5988-4114-a5e4-5277211408bf" />

__3.__ __Update the package database and install MongoDB__

```
sudo apt-get update
```
<img width="900" alt="updatemongo" src="https://github.com/user-attachments/assets/d18cbce0-5b15-41e4-8033-e953cf0025de" />

```
sudo apt-get install -y mongodb-org
```
<img width="900" alt="mongodbinst" src="https://github.com/user-attachments/assets/e6a3472d-fccc-4292-939a-2918a28aa21e" />

__4.__ __Start and enable MongoDB__

```
sudo systemctl enable --now mongod
systemctl status mongod
```
<img width="900" alt="mongorun" src="https://github.com/user-attachments/assets/ed2a162f-9484-40ff-ac1f-964ec5f6d553" />

__5.__ __Install body-parser package__

__body-parser__ package is needed to help process JSON files passed in requests to the server.

```
sudo npm install body-parser
```
<img width="900" alt="bodyparser" src="https://github.com/user-attachments/assets/c8c77a9c-ccab-4c42-9a14-f36878fee75e" />

__6.__ __Create the project root folder named ‘Books’__

```
mkdir Books && cd Books
```

Initialize the root folder

```
npm init
```
<img width="900" alt="books" src="https://github.com/user-attachments/assets/b5b245bc-493b-4479-a983-aa0a4bdbe173" />

__Add file named server.js to Books folder__

```
vim server.js
```
Copy and paste the web server code below into the server.js file.

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose'); // Make sure mongoose is installed and required
const path = require('path'); // To handle static file serving
const app = express();

// Connect to MongoDB
mongoose.connect('mongodb://127.0.0.1:27017/test')
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error('MongoDB connection error:', err));

// Middleware
app.use(bodyParser.json());
app.use(express.static(path.join(__dirname, 'public')));

// Routes
require('./apps/routes')(app);

// Start the server
app.set('port', 3300);
app.listen(app.get('port'), () => {
  console.log('Server up: http://localhost:' + app.get('port'));
});
```
<img width="900" alt="vim" src="https://github.com/user-attachments/assets/5f99f530-c742-4308-980c-7c955b07f4c7" />

## Step 3 - Install Express and set up routes to the server

Express was used to pass book information to and from our MongoDB database.
Mongoose package provides a straightforward schema-based solution to model the application data. Mongoose was used to establish a schema for the database to store data of the book register.

__1.__ __Install express and mongoose__

```
sudo npm install express mongoose
```
<img width="900" alt="mangoose" src="https://github.com/user-attachments/assets/c45efdb3-7790-4267-861e-14737627a5da" />

__2.__ __In Books folder, create a folder named ‘apps’__

```
mkdir apps && cd apps
```
__In apps, create a file named routes.js__

```
vim routes.js
```
Copy and paste the code below into routes.js

```
const Book = require('./models/books');
const path = require('path');

module.exports = function(app) {
  // Get all books
  app.get('/book', async (req, res) => {
    try {
      const books = await Book.find({});
      res.json(books);
    } catch (err) {
      console.error(err);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  });

  // Add a new book
  app.post('/book', async (req, res) => {
    try {
      const book = new Book({
        name: req.body.name,
        isbn: req.body.isbn,
        author: req.body.author,
        pages: req.body.pages
      });
      const result = await book.save();
      res.json({
        message: "Successfully added book",
        book: result
      });
    } catch (err) {
      console.error(err);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  });

  // Update a book
  app.put('/book/:isbn', async (req, res) => {
    try {
      const updatedBook = await Book.findOneAndUpdate(
        { isbn: req.params.isbn },
        req.body,
        { new: true }
      );
      if (!updatedBook) {
        return res.status(404).json({ error: 'Book not found' });
      }
      res.json({
        message: "Successfully updated the book",
        book: updatedBook
      });
    } catch (err) {
      console.error(err);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  });

  // Delete a book
  app.delete('/book/:isbn', async (req, res) => {
    try {
      const result = await Book.findOneAndRemove({ isbn: req.params.isbn });
      if (!result) {
        return res.status(404).json({ error: 'Book not found' });
      }
      res.json({
        message: "Successfully deleted the book",
        book: result
      });
    } catch (err) {
      console.error(err);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  });


  // ALL API routes above here
   // fallback route (NO PATH STRING)
app.use((req, res) => {
  res.sendFile(path.join(__dirname, '../public', 'index.html'));
});
};
```
<img width="900" alt="app" src="https://github.com/user-attachments/assets/d0205c97-a01d-4446-8beb-59f9612004a2" />

__3.__ __In the ‘apps’ folder, create a folder named models__

```
mkdir models && cd models
```
__In models, create a file named book.js__

```
vim book.js
```
Copy and paste the code below into book.js

```
const mongoose = require('mongoose');

const bookSchema = new mongoose.Schema({
  name: { type: String, required: true },
  isbn: { type: String, required: true, unique: true },
  author: { type: String, required: true },
  pages: { type: Number, required: true }
});

module.exports = mongoose.model('Book', bookSchema);
```
<img width="900" alt="booksjs" src="https://github.com/user-attachments/assets/3c083d55-e79c-45f5-a39d-5c02ff3efd7d" />

## Step 4 - Access the routes with AngularJS

In this project, AngularJS was used to connect the web page with Express and perform actions on the book register.

__1.__ __Change the directory back to ‘Books’ and create a folder named ‘public’__

```
cd ../..

mkdir public && cd public
```
__Add a file named script.js into public folder__

```
vim script.js
```
Copy and paste the code below (controller configuration defined) into the script.js file.

```
var app = angular.module('myApp', []);

app.controller('myCtrl', function($scope, $http) {
  // Get all books
  function getAllBooks() {
    $http({
      method: 'GET',
      url: '/book'
    }).then(function successCallback(response) {
      $scope.books = response.data;
    }, function errorCallback(response) {
      console.log('Error: ' + response.data);
    });
  }

  // Initial load of books
  getAllBooks();

  // Add a new book
  $scope.add_book = function() {
    var body = {
      name: $scope.Name,
      isbn: $scope.Isbn,
      author: $scope.Author,
      pages: $scope.Pages
    };
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response.data);
      getAllBooks();  // Refresh the book list
      // Clear the input fields
      $scope.Name = '';
      $scope.Isbn = '';
      $scope.Author = '';
      $scope.Pages = '';
    }, function errorCallback(response) {
      console.log('Error: ' + response.data);
    });
  };

  // Update a book
  $scope.update_book = function(book) {
    var body = {
      name: book.name,
      isbn: book.isbn,
      author: book.author,
      pages: book.pages
    };
    $http({
      method: 'PUT',
      url: '/book/' + book.isbn,
      data: body
    }).then(function successCallback(response) {
      console.log(response.data);
      getAllBooks();  // Refresh the book list
    }, function errorCallback(response) {
      console.log('Error: ' + response.data);
    });
  };

  // Delete a book
  $scope.delete_book = function(isbn) {
    $http({
      method: 'DELETE',
      url: '/book/' + isbn
    }).then(function successCallback(response) {
      console.log(response.data);
      getAllBooks();  // Refresh the book list
    }, function errorCallback(response) {
      console.log('Error: ' + response.data);
    });
  };
});
```
<img width="900" alt="script" src="https://github.com/user-attachments/assets/8c1fb44c-aadc-48d5-b22c-ff787e1a4087" />

__2.__ __In ‘public’ folder, create a file named index.html__

```
vim index.html
```
Copy and paste the code below into index.html file

```
<!DOCTYPE html>
<html ng-app="myApp" ng-controller="myCtrl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Book Management</title>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.8.2/angular.min.js"></script>
  <script src="script.js"></script>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    table { border-collapse: collapse; width: 100%; }
    th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
    th { background-color: #f2f2f2; }
    input[type="text"], input[type="number"] { width: 100%; padding: 5px; }
    button { margin-top: 10px; padding: 5px 10px; }
  </style>
</head>
<body>
  <h1>Book Management</h1>
  
  <h2>Add New Book</h2>
  <form ng-submit="add_book()">
    <table>
      <tr>
        <td>Name:</td>
        <td><input type="text" ng-model="Name" required></td>
      </tr>
<tr>
        <td>ISBN:</td>
        <td><input type="text" ng-model="Isbn" required></td>
      </tr>
      <tr>
        <td>Author:</td>
        <td><input type="text" ng-model="Author" required></td>
      </tr>
      <tr>
        <td>Pages:</td>
        <td><input type="number" ng-model="Pages" required></td>
      </tr>
    </table>
    <button type="submit">Add Book</button>
  </form>

  <h2>Book List</h2>
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>ISBN</th>
        <th>Author</th>
        <th>Pages</th>
        <th>Action</th>
      </tr>
    </thead>
    <tbody>
      <tr ng-repeat="book in books">
        <td>{{book.name}}</td>
        <td>{{book.isbn}}</td>
        <td>{{book.author}}</td>
        <td>{{book.pages}}</td>
        <td><button ng-click="del_book(book)">Delete</button></td>
      </tr>
    </tbody>
  </table>
</body>
</html>
```
<img width="900" alt="html" src="https://github.com/user-attachments/assets/e8ec0827-bba7-462c-b3d7-3ed1423087ba" />

__3.__ __Change the directory back up to ‘Books’ and start the server__

```
cd ..

node server.js
```
<img width="900" alt="server" src="https://github.com/user-attachments/assets/a92bfe1e-715b-40ed-9e3a-02e1820cadbb" />

The server is now up and running, Connection to it is via port 3300. A separate Putty or SSH console to test what curl command returns locally can be launched.

The Book Register web application can now be accessed from the internet with a browser using the Public IP address or Public DNS name.

<img width="900" alt="http" src="https://github.com/user-attachments/assets/0a9a1902-1cc2-40c5-9360-76a82d286869" />

Add more books to the register

<img width="900"  alt="addpages" src="https://github.com/user-attachments/assets/cc5356d1-afce-45e6-9e89-f2d459266269" />

<img width="900" alt="addb" src="https://github.com/user-attachments/assets/19c6fcc4-1e2e-42ec-a39f-4a49179e2ffe" />

Get the json view

<img width="900" alt="json" src="https://github.com/user-attachments/assets/2eab5fa4-ef28-4c36-9e0a-1daf1ce8fde3" />

## Conclusion

Mean stack is a strong choice for developing cloud-native applications because of its scalability and its ability to manage concurrent users. The AngularJS front end framework also makes it ideal for developing single-page applications (SPAs) that serve all information and functionality on a single page.
