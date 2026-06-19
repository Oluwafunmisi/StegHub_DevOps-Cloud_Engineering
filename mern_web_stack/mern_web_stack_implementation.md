### Introduction

__The MERN stack is a highly popular, open-source full-stack JavaScript framework used to build modern, scalable web applications. It is an acronym for its four core technologies: MongoDB, Express.js, React, and Node.js. By using JavaScript across both the frontend and backend, it eliminates the need for context switching between different programming languages during development.__

Core components of mern web stack are:

__MongoDB:__ A NoSQL, document-oriented database that stores data in flexible, JSON-like formats.

__Express.js:__ A lightweight backend web application framework built on top of Node.js to manage server routing and APIs.

__React.js:__ A powerful frontend JavaScript library used for building interactive, component-based user interfaces.

__Node.js:__  A runtime environment that executes JavaScript code on the server side, handling concurrent connections efficiently.__

__This guide provides a comprehensive overview of setting up and utilizing each component of the MERN stack, to develop robust web applications.__


## Step 0: Prerequisites

__1.__ EC2 Instance of t3.micro type and Ubuntu 26.04 LTS (HVM) was lunched in the eu-north-1 region using the AWS console.

<img width="800"  alt="createinstance" src="https://github.com/user-attachments/assets/78e6edc0-4958-4b79-8b54-5e4fa312e616" />
<img width="800"  alt="instancecreated" src="https://github.com/user-attachments/assets/80a9a500-b800-48d9-89ff-2b71b527e79e" />

__Add tag "Name"__

<img width="800"  alt="tagname" src="https://github.com/user-attachments/assets/0a740f99-81f0-4f63-bc89-04330d9a5edf" />

__2.__ Attached SSH key named __test.pem__ to access the instance on port 22

__3.__ The security group was configured with the following inbound rules:

- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.

- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.

- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.
  
<img width="800" alt="inboundroles" src="https://github.com/user-attachments/assets/1aa7837d-5f51-4e92-8c1a-0462f5306b03" />

__4.__ The private ssh key  was used to connect to the instance by running

```
ssh -i test.pem ubuntu@13.60.65.97
```
Where __username=ubuntu__ and __public ip address=13.60.65.97__

<img width="500" alt="connectinstance" src="https://github.com/user-attachments/assets/adf55a86-a177-4709-af75-04b325110025" />

## Step 1 - Backend Configuration

__1.__ __Update and upgrade Ubuntu__

```
sudo apt update && sudo apt upgrade -y
```
<img width="700" alt="ubuntu" src="https://github.com/user-attachments/assets/c8ceec06-7b9c-4c9b-8bf8-3e3189bbd75c" />

__2.__ __Get the location of Node.js software from ubuntu repositories__.

```
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
```
<img width="800" alt="location" src="https://github.com/user-attachments/assets/5176b95e-aff0-49cc-b61d-e8f5e22325a8" />

__3.__ __Install node.js on server__.

```
sudo apt-get install nodejs -y
```
<img width="700" alt="installnode" src="https://github.com/user-attachments/assets/ffcf5606-1cd3-44ef-bddf-2af5925c01d0" />

__4.__ __Verify the Node installation with the command below__.

```
node -v        // Gives the node version

npm -v        // Gives the node package manager version
```
<img width="261" alt="nodeversion" src="https://github.com/user-attachments/assets/bd8a3e6c-bc0d-456b-a298-066a82dd981f" />

### Application Code Setup

__1.__ __Create a new directory for the TO-DO project, switch to the new directory and then initialize the project directory.__
```
mkdir Todo && ls && cd Todo

npm init
```
Follow the prompts after running the command. You can press “Enter” several times to accept default values, then accept to write out the package.json file by typing yes.

<img width="556" alt="todo" src="https://github.com/user-attachments/assets/45c4ea8d-5c5c-429a-b68c-e29b7aa1d318" />

### Install ExpressJs

Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.

__1.__ __Install Express using npm__

```
npm install express
```
<img width="372" alt="npm" src="https://github.com/user-attachments/assets/d149fdfb-8b92-4931-86c1-9971e2759168" />

__2.__ __Create a file index.js and run ls to confirm the file__

```
touch index.js && ls
```
<img width="400" alt="js" src="https://github.com/user-attachments/assets/6ebf6d8d-175a-409e-947b-41e6c3117109" />

__3.__ __Install dotenv module__

```
npm install dotenv
```
<img width="376" height="122" alt="dotenv" src="https://github.com/user-attachments/assets/0a73e325-d96f-440e-a41d-8dfed6886a99" />

The next step requires you to add inbound rules in your E2 instance

- Allow traffic on port 5000 (Custom TCP) with source from anywhere.

- Allow traffic on port 3000 (Custom TCP) with sourec from anywhere.
  
<img width="800" alt="addrules" src="https://github.com/user-attachments/assets/1ed31e41-b4a1-45d0-b3db-bf6b9656d521" />
<img width="901" height="217" alt="newrule" src="https://github.com/user-attachments/assets/b514e9a6-1a3f-44b4-848b-564ce0b6fbda" />

  
__4.__ __Open index.js file__
```
vim index.js
```
It should open and empty page, type the code below into it

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
<img width="700" src="https://github.com/user-attachments/assets/49c8470a-b8fd-4048-b091-ba5aa4438d4a" />

__5.__ __Start the server to see if it works. Open your terminal in the same directory as your index.js file. Run__

```
node index.js
```
<img width="534" alt="server" src="https://github.com/user-attachments/assets/2bd173eb-5d8a-4c99-9fe6-4a465f4d1c5e" />

Port 5000 has been opened in ec2 security group.

__Access the server with the public IP followed by the port__

```
http://13.60.65.97:5000
```
<img width="219" alt="express" src="https://github.com/user-attachments/assets/911cbaba-d763-4ab6-b80f-6069918392f0" />

## Routes

There are three actions that the ToDo application needs to be able to do:
- Create a new task
- Display list of all task
- Delete a completed task

Each task was associated with some particular endpoint and used different standard __HTTP__ request methods: __POST__, __GET__, __DELETE__.

For each task, routes were created which defined various endpoints that the ToDo app depends on.

__1.__ __Create a folder routes, switch to routes directory and create a file api.js. Open the file__

```
mkdir routes && cd routes && touch api.js
vim api.js
```
__Copy__ the code below into the file

```bash
const express = require('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

});

module.exports = router;
```
<img width="391" alt="todos" src="https://github.com/user-attachments/assets/abc78b36-f06c-46df-834b-5ceb820dacf8" />

## Models

A model is at the heart of JavaScript based applications and it is what makes it interactive.

Models was used to define the database schema. This is important in order be able to define the fields stored in each Mongodb document.

In essence, the schema is a blueprint of how the database is constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties.
To create a schema and a model, mongoose  was installed, which is a Node.js package that makes working with mongodb easier.

__1.__ __Change the directory back to Todo folder and install mongoose__

```
npm install mongoose
```
<img width="378" alt="mongoose" src="https://github.com/user-attachments/assets/90eb2e76-7e36-4699-9c7d-497036c8a4bc" />

__2.__ __Create a new folder models, switch to models directory, create a file todo.js inside models. Open the file__

```
mkdir models && cd models && touch todo.js

vim todo.js
```
Past the code below into the file

```
import mongoose from 'mongoose';

const Schema = mongoose.Schema;

const TodoSchema = new Schema({
  action: {
    type: String,
    required: [true, 'The todo text field is required']
  }
});

const Todo = mongoose.model('todo', TodoSchema);

export default Todo;
```
<img width="600" alt="requiremoon" src="https://github.com/user-attachments/assets/f71d348e-0e84-4380-a71b-42e5af1d89a8" />

The routes was updated from the file api.js in the ‘routes’ directory to make use of the new model.

__3.__ __In Routes directory, open api.js and delete the code inside with :%d__.

```
vim api.js
```

Paste the new code below into it
```
import express from 'express';
import Todo from '../models/todo.js';

const router = express.Router();

router.get('/todos', (req, res, next) => {
  Todo.find({}, 'action')
    .then(data => res.json(data))
    .catch(next);
});

router.post('/todos', (req, res, next) => {
  if (req.body.action) {
    Todo.create(req.body)
      .then(data => res.json(data))
      .catch(next);
  } else {
    res.json({ error: "The input field is empty" });
  }
});

router.delete('/todos/:id', (req, res, next) => {
  Todo.findOneAndDelete({ _id: req.params.id })
    .then(data => res.json(data))
    .catch(next);
});

export default router;
```
<img width="600" alt="requireexpress" src="https://github.com/user-attachments/assets/4220fea8-a6ea-4243-87ae-0316732159db" />

## MongoDB Database

__mLab__ provides MongoDB database as a service solution (DBaaS). MongoDB has two cloud database management system components: mLab and Atlas, Both were formerly cloud databases managed by MongoDB (MongoDB acquired mLab in 2018, with certain differences). In November, MongoDB merged the two cloud databases and as such, __mLab.com__ redirects to the __MongoDB Atlas website__.

__1.__ __Create a MongoDB database and collection inside atlas__

MongoDB Cluster Overview

<img width="700" alt="clusteroverview" src="https://github.com/user-attachments/assets/7e69af81-5d71-404a-b4be-acd28a7ca530" />

AWS cloud provider, in region eu-north-1 was selected.

<img width="600" alt="eu" src="https://github.com/user-attachments/assets/aaad7e78-34b5-44ca-88e7-bc2b777dc9be" />

A __database__ named __tododb__ and __collections__ named __todo__ was created.

<img width="800" alt="tododb" src="https://github.com/user-attachments/assets/0614e429-a574-4be8-bb4d-447cd5525622" />
<img width="800" alt="ipactive" src="https://github.com/user-attachments/assets/3fac3ef5-1c1b-466b-a3f9-eae75be1e9cd" />

__2.__ __Create a file in your Todo directory and name it .env, open the file__
```
touch .env && vim .env
```
Add connection string below to access the database

```
DB = ‘mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority’
```
<img width="800" alt="dbread" src="https://github.com/user-attachments/assets/0c69fe30-3c67-4edf-9ada-27d984ff51ad" />

__3.__ __Update the index.js to reflect the use of .env so that Node.js can connect to the database__.

```
vim index.js
```
Delete existing content in the file, and update it with the entire code below:

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

// Connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log(`Database connected successfully`))
  .catch(err => console.log(err));

// Since mongoose promise is deprecated, we override it with Node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
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
  console.log(`Server running on port ${port}`);
});

```
<img width="700" alt="nodedb" src="https://github.com/user-attachments/assets/ed066ea5-c076-4610-a2be-06aafcfac9e6" />

__4.__ __Start your server using the command__
```
node index.js
```
Server is running but there was a mongo parse error
<img width="800" alt="serverdb" src="https://github.com/user-attachments/assets/ecdeb98f-d630-4d2b-85fa-ae5d3c3e115f" />

To fix this error I modified the code to:

```
import express from 'express';
import dotenv from 'dotenv';
import bodyParser from 'body-parser';
import mongoose from 'mongoose';

import routes from './routes/api.js';
export default router;

dotenv.config({ path: './.env' });
dotenv.config();

const app = express();

const port = process.env.PORT || 5000;

// Connect to database

mongoose.connect(process.env.DB)
  .then(() => console.log('Database connected successfully'))
  .catch(err => console.error('Mongo error:', err));

mongoose.Promise = global.Promise;

app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header(
    'Access-Control-Allow-Headers',
    'Origin, X-Requested-With, Content-Type, Accept'
  );
  next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
  console.error(err);
  next(err);
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```
<img width="800" alt="dbconnected" src="https://github.com/user-attachments/assets/21aea817-9f64-407d-8868-f092d94b5556" />

## Testing Backend Code without Frontend using RESTful API

Postman was used to test the backend code.
The endpoints were tested. For the endpoints that require body, JSON was sent back with the necessary fields since it’s what was set up in the code.

__1.__ __Open Postman and Set the header__

```
http://13.60.65.975000/api/todos
```
