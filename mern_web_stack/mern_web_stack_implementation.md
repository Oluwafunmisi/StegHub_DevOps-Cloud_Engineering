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

<img width="900"  alt="createinstance" src="https://github.com/user-attachments/assets/78e6edc0-4958-4b79-8b54-5e4fa312e616" />

<img width="900"  alt="instancecreated" src="https://github.com/user-attachments/assets/80a9a500-b800-48d9-89ff-2b71b527e79e" />

__Add tag "Name"__

<img width="900"  alt="tagname" src="https://github.com/user-attachments/assets/0a740f99-81f0-4f63-bc89-04330d9a5edf" />

__2.__ Attached SSH key named __test.pem__ to access the instance on port 22

__3.__ The security group was configured with the following inbound rules:

- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.

- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.

- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.
  
<img width="900" alt="inboundroles" src="https://github.com/user-attachments/assets/1aa7837d-5f51-4e92-8c1a-0462f5306b03" />

__4.__ The private ssh key  was used to connect to the instance by running

```
ssh -i test.pem ubuntu@13.60.65.97
```
Where __username=ubuntu__ and __public ip address=13.60.65.97__

<img width="900" alt="connectinstance" src="https://github.com/user-attachments/assets/adf55a86-a177-4709-af75-04b325110025" />

## Step 1 - Backend Configuration

__1.__ __Update and upgrade Ubuntu__

```
sudo apt update && sudo apt upgrade -y
```
<img width="900" alt="ubuntu" src="https://github.com/user-attachments/assets/c8ceec06-7b9c-4c9b-8bf8-3e3189bbd75c" />

__2.__ __Get the location of Node.js software from ubuntu repositories__.

```
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
```
<img width="900" alt="location" src="https://github.com/user-attachments/assets/5176b95e-aff0-49cc-b61d-e8f5e22325a8" />

__3.__ __Install node.js on server__.

```
sudo apt-get install nodejs -y
```
<img width="900" alt="installnode" src="https://github.com/user-attachments/assets/ffcf5606-1cd3-44ef-bddf-2af5925c01d0" />

__4.__ __Verify the Node installation with the command below__.

```
node -v        // Gives the node version

npm -v        // Gives the node package manager version
```
<img width="900" alt="nodeversion" src="https://github.com/user-attachments/assets/bd8a3e6c-bc0d-456b-a298-066a82dd981f" />

### Application Code Setup

__1.__ __Create a new directory for the TO-DO project, switch to the new directory and then initialize the project directory.__
```
mkdir Todo && ls && cd Todo

npm init
```
Follow the prompts after running the command. You can press “Enter” several times to accept default values, then accept to write out the package.json file by typing yes.

<img width="900" alt="todo" src="https://github.com/user-attachments/assets/45c4ea8d-5c5c-429a-b68c-e29b7aa1d318" />

### Install ExpressJs

Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.

__1.__ __Install Express using npm__

```
npm install express
```
<img width="900" alt="npm" src="https://github.com/user-attachments/assets/d149fdfb-8b92-4931-86c1-9971e2759168" />

__2.__ __Create a file index.js and run ls to confirm the file__

```
touch index.js && ls
```
<img width="900" alt="js" src="https://github.com/user-attachments/assets/6ebf6d8d-175a-409e-947b-41e6c3117109" />

__3.__ __Install dotenv module__

```
npm install dotenv
```
<img width="900" alt="dotenv" src="https://github.com/user-attachments/assets/0a73e325-d96f-440e-a41d-8dfed6886a99" />

The next step requires you to add inbound rules in your E2 instance

- Allow traffic on port 5000 (Custom TCP) with source from anywhere.

- Allow traffic on port 3000 (Custom TCP) with sourec from anywhere.
  
<img width="900" alt="addrules" src="https://github.com/user-attachments/assets/1ed31e41-b4a1-45d0-b3db-bf6b9656d521" />

<img width="900"  alt="newrule" src="https://github.com/user-attachments/assets/b514e9a6-1a3f-44b4-848b-564ce0b6fbda" />

  
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
<img width="900" src="https://github.com/user-attachments/assets/49c8470a-b8fd-4048-b091-ba5aa4438d4a" />

__5.__ __Start the server to see if it works. Open your terminal in the same directory as your index.js file. Run__

```
node index.js
```
<img width="900" alt="server" src="https://github.com/user-attachments/assets/2bd173eb-5d8a-4c99-9fe6-4a465f4d1c5e" />

Port 5000 has been opened in ec2 security group.

__Access the server with the public IP followed by the port__

```
http://13.60.65.97:5000
```
<img width="900" alt="express" src="https://github.com/user-attachments/assets/911cbaba-d763-4ab6-b80f-6069918392f0" />

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

```
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
<img width="900" alt="todos" src="https://github.com/user-attachments/assets/f98c9c1d-d246-4a0d-815c-ded42a39bc1a" />

## Models

A model is at the heart of JavaScript based applications and it is what makes it interactive.

Models was used to define the database schema. This is important in order be able to define the fields stored in each Mongodb document.

In essence, the schema is a blueprint of how the database is constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties.
To create a schema and a model, mongoose  was installed, which is a Node.js package that makes working with mongodb easier.

__1.__ __Change the directory back to Todo folder and install mongoose__

```
npm install mongoose
```
<img width="900" alt="mongoose" src="https://github.com/user-attachments/assets/90eb2e76-7e36-4699-9c7d-497036c8a4bc" />

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
<img width="900" alt="requiremoon" src="https://github.com/user-attachments/assets/f71d348e-0e84-4380-a71b-42e5af1d89a8" />

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
<img width="900" alt="requireexpress" src="https://github.com/user-attachments/assets/4220fea8-a6ea-4243-87ae-0316732159db" />

## MongoDB Database

__mLab__ provides MongoDB database as a service solution (DBaaS). MongoDB has two cloud database management system components: mLab and Atlas, Both were formerly cloud databases managed by MongoDB (MongoDB acquired mLab in 2018, with certain differences). In November, MongoDB merged the two cloud databases and as such, __mLab.com__ redirects to the __MongoDB Atlas website__.

__1.__ __Create a MongoDB database and collection inside atlas__

MongoDB Cluster Overview

<img width="900" alt="clusteroverview" src="https://github.com/user-attachments/assets/7e69af81-5d71-404a-b4be-acd28a7ca530" />

AWS cloud provider, in region eu-north-1 was selected.

<img width="900" alt="eu" src="https://github.com/user-attachments/assets/aaad7e78-34b5-44ca-88e7-bc2b777dc9be" />

A __database__ named __tododb__ and __collections__ named __todos__ was created.

<img width="900" alt="tododb" src="https://github.com/user-attachments/assets/e89edf71-0b42-4a7d-bc11-913f00ce2196" />

<img width="900" alt="ipactive" src="https://github.com/user-attachments/assets/3fac3ef5-1c1b-466b-a3f9-eae75be1e9cd" />

__2.__ __Create a file in your Todo directory and name it .env, open the file__
```
touch .env && vim .env
```
Add connection string below to access the database

```
DB = ‘mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority’
```
<img width="900" alt="dbread" src="https://github.com/user-attachments/assets/9fe5cefb-6bbe-4c0d-b8ba-9adc652acbc4" />

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
<img width="900" alt="nodedb" src="https://github.com/user-attachments/assets/ed066ea5-c076-4610-a2be-06aafcfac9e6" />

__4.__ __Start your server using the command__
```
node index.js
```
Server is running but there was a mongo parse error
<img width="900" alt="serverdb" src="https://github.com/user-attachments/assets/ecdeb98f-d630-4d2b-85fa-ae5d3c3e115f" />

To fix this error I modified the code to:

```
import express from 'express';
import dotenv from 'dotenv';
import bodyParser from 'body-parser';
import mongoose from 'mongoose';

import routes from './routes/api.js';

dotenv.config({ path: './.env' });
dotenv.config();

const app = express();

const port = process.env.PORT || 5000;

// Connect to database

mongoose.connect(process.env.DB)
  .then(() => {
    console.log('Database connected successfully');
    console.log('Database name:', mongoose.connection.name);
  })
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
<img width="900" alt="dbconnected" src="https://github.com/user-attachments/assets/1a2028c7-b1b6-41d1-ba94-748ee014acb6" />

## Testing Backend Code without Frontend using RESTful API

Postman was used to test the backend code.
The endpoints were tested. For the endpoints that require body, JSON was sent back with the necessary fields since it’s what was set up in the code.

__1.__ __Open Postman and Set the header__

```
http://13.60.65.97:5000/api/todos
```
<img width="900" alt="header" src="https://github.com/user-attachments/assets/cc3a22ed-ea6b-4f20-8bce-d218f87cb339" />

### Create POST requests to the API

<img width="900" alt="post" src="https://github.com/user-attachments/assets/1efdce26-d49f-4b52-81ee-1d8d8059b77f" />

<img width="900" alt="posttt" src="https://github.com/user-attachments/assets/2af96a31-5d36-4517-a077-b21b3b93534b" />

<img width="900" alt="postt" src="https://github.com/user-attachments/assets/d696b86a-d7cb-44f0-956d-5c7637bd84e0" />

### Check Database Collections

<img width="900" alt="dbtodos" src="https://github.com/user-attachments/assets/418fe514-d296-4c31-acda-37e98f2a8c7a" />

<img width="900" alt="dbtodosss" src="https://github.com/user-attachments/assets/1d579b5c-d58f-4dc9-8727-d423b56ced80" />

<img width="900" alt="dbtodoss" src="https://github.com/user-attachments/assets/7cf05944-aa06-4b36-8991-94b9b1f90224" />

### Make a GET requests to the API

This request retrieves all existing records from our To-Do application (backend requests these records from the database and sends us back as a response to GET request).

<img width="900" alt="get" src="https://github.com/user-attachments/assets/56808d82-b786-4529-9d05-f603ce461d4c" />

<img width="900" alt="gettt" src="https://github.com/user-attachments/assets/eb29f014-4eb0-48e3-9e99-68f2a0e09390" />

<img width="900" alt="gett" src="https://github.com/user-attachments/assets/f5a6afbc-2a80-4908-a21b-74f618dba661" />


###  Delete Database Collections

<img width="900" alt="delete" src="https://github.com/user-attachments/assets/c5fa3f6c-6e5b-4221-a8e1-f234ac18f14e" />

###  Check Database Collections

<img width="900" alt="check" src="https://github.com/user-attachments/assets/cebcd023-8173-425f-a1d7-55ef75363b71" />

### Make another GET requests to the API

<img width="900" alt="newg" src="https://github.com/user-attachments/assets/9c558914-be6f-4dd5-9110-5527e970a758" />

## Step 2 - Frontend Creation

It is time to create a user interface for a Web client (browser) to interact with the application via API

__1.__ __In the same root directory as your backend code, which is the Todo directory, run:__

```
npx create-react-app client
```
<img width="800"  alt="react" src="https://github.com/user-attachments/assets/fbce7cde-2424-4bf1-a4e0-621e3600f278" />


This created a new folder in the Todo directory called client, where all the react code was added.

### Running a React App

Before testing the react app, the following dependencies needs to be installed in the project root directory.

- __Install concurrently__. It is used to run more than one command simultaneously from the same terminal window.
  
```
npm install concurrently --save-dev
```
<img width="900" alt="save" src="https://github.com/user-attachments/assets/27a497b7-e3de-435b-9dc3-90d50397fd74" />

- __Install nodemon__. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
  
```
npm install nodemon --save-dev
```
<img width="900" alt="nodemon" src="https://github.com/user-attachments/assets/541324c4-7acb-4fd5-b9a0-e31dbaa0ab60" />

- In Todo folder open the package.json file, change the highlighted part of the below screenshot and replace with the code below:
- 
```
"scripts": {
  "start": "node index.js",
  "start-watch": "nodemon index.js",
  "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
}
```
<img width="900" alt="package" src="https://github.com/user-attachments/assets/24bc0976-17b9-40b5-92b4-65ab5cfc27bc" />


### Configure Proxy In package.json

- Change directory to “client”
```
cd client
```
- Open the package.json file
```
vim package.json
```
<img width="900" alt="package" src="https://github.com/user-attachments/assets/d7e02fc8-b6d6-448c-af40-4e4105b0a568" />

Add the key value pair in the package.json file
```
“proxy”: “http://localhost:5000”
```
<img width="900" alt="proxy" src="https://github.com/user-attachments/assets/7a01bced-84c0-4779-9457-93ba56c2570d" />

The whole purpose of adding the proxy configuration above is to make it possible to access the application directly from the browser by simply calling the server url like
http://locathost:5000 rather than always including the entire path like http://localhost:5000/api/todos

Ensure you are inside the Todo directory, and simply do:
```
npm run dev
```
<img width="900" alt="run" src="https://github.com/user-attachments/assets/6964d052-5130-4a34-b1dd-db5af432228d" />

The app opened and started running on localhost:3000

__Note__: In order to access the application from the internet, TCP port 3000 had been opened on EC2.


## Creating React Components

One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For the Todo app, there are two stateful components and one stateless component. From Todo directory, run:

```
cd client
```
Move to the “src” directory
```
cd src
```

__2.__ __Inside your src folder, create another folder called “components”__

```
mkdir components
```
Move into the components directory

```
cd components
```
<img width="900" alt="components" src="https://github.com/user-attachments/assets/bf09e147-3340-4e7f-aea3-f47c0278ff2c" />

__3.__ __Inside the ‘components’ directory create three files “Input.js”, “ListTodo.js” and “Todo.js”.__

```
touch Input.js ListTodo.js Todo.js
```
<img width="900" alt="tilt" src="https://github.com/user-attachments/assets/303f6cd1-3244-4835-8abe-60102ff62a07" />

#### Open Input.js file
```
vim Input.js
```
Paste in the following:

```
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {
  state = {
    action: ""
  }

  handleChange = (event) => {
    this.setState({ action: event.target.value });
  }

  addTodo = () => {
    const task = { action: this.state.action };

    if (task.action && task.action.length > 0) {
      axios.post('/api/todos', task)
        .then(res => {
          if (res.data) {
            this.props.getTodos();
            this.setState({ action: "" });
          }
        })
        .catch(err => console.log(err));
    } else {
      console.log('Input field required');
    }
  }

  render() {
    let { action } = this.state;
    return (
      <div>
        <input type="text" onChange={this.handleChange} value={action} />
        <button onClick={this.addTodo}>add todo</button>
      </div>
    );
  }
}

export default Input;
```
<img width="900" alt="inputjs" src="https://github.com/user-attachments/assets/aac5cf09-1ef3-48f8-a122-1d46e81cc634" />

In oder to make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.

Move to the client folder
```
cd ../..
```
__Install Axios__

```
npm install axios
```
<img width="900" alt="axios" src="https://github.com/user-attachments/assets/e8779584-ce2c-431a-8711-25ea25dc1ff7" />

#### Go to components directory

```
cd src/components
```
#### After that open the ListTodo.js

```
vim ListTodo.js
```
Copy and paste the following code:

```
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {
  return (
    <ul>
      {
        todos && todos.length > 0 ? (
          todos.map(todo => {
            return (
              <li key={todo._id} onClick={() => deleteTodo(todo._id)}>
                {todo.action}
              </li>
            );
          })
        ) : (
          <li>No todo(s) left</li>
        )
      }
    </ul>
  );
}

export default ListTodo;
```
<img width="900" alt="listjs" src="https://github.com/user-attachments/assets/ddd11a8b-75a6-4dfe-b2af-4cdc6fc4d8cc" />

#### Then in the Todo.js file, write the following code

```
vim Todo.js
```
Paste inside:

```
import React, { Component } from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {
  state = {
    todos: []
  }

  componentDidMount() {
    this.getTodos();
  }

  getTodos = () => {
    axios.get('/api/todos')
      .then(res => {
        if (res.data) {
          this.setState({
            todos: res.data
          });
        }
      })
      .catch(err => console.log(err));
  }

  deleteTodo = (id) => {
    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if (res.data) {
          this.getTodos();
        }
      })
      .catch(err => console.log(err));
  }

  render() {
    let { todos } = this.state;
    return (
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos} />
        <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
      </div>
    );
  }
}

export default Todo;
```
<img width="900" alt="todojs" src="https://github.com/user-attachments/assets/84ee521f-4021-40bf-a019-1439908b47fb" />

__We need to make a little adjustment to our react code. Delete the logo and adjust our App.js to look like this__

### Move to src folder

```
cd ..
```

Ensure to be in the src folder and run:

```
vim App.js
```
#### Copy and paste the following code

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
<img width="900" alt="appjs" src="https://github.com/user-attachments/assets/70287e4a-bb5c-4f68-a1b5-baaf3ebbe05a" />

####  In the src directory, open the App.css

```
vim App.css
```

Paste the following code into it

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
    width: 100%;
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
<img width="900" alt="appcss" src="https://github.com/user-attachments/assets/50549fe2-ca3d-427d-848b-3b165dd5b88a" />

#### In the src directory, open the index.css

```
vim index.css
```
#### Copy and paste the code below:

```
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  box-sizing: border-box;
  background-color: #282c34;
  color: #787a80;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New", monospace;
}
```
<img width="900" alt="indexcss" src="https://github.com/user-attachments/assets/066acc1c-5fc3-49f8-9095-c826b0e68bc8" />

#### Go to the Todo directory

```
cd ../..
```

Run:

```
npm run dev
```
<img width="900" alt="sus" src="https://github.com/user-attachments/assets/c7e312e8-b08a-4c38-9948-b6ae98131f81" />

At this point, the To-Do app is ready and fully functional with the functionality discussed earlier: Creating a task, deleting a task, and viewing all the tasks.

__The client can now be viewed in the browser__

<img width="900" alt="btodo" src="https://github.com/user-attachments/assets/7400c723-7133-4447-82b0-d7fd2c8f3dc0" />

__Add some todos via the browser .__

<img width="900" alt="st" src="https://github.com/user-attachments/assets/298755ba-550b-482f-9ec4-1aa6a24510f6" />

<img width="900" alt="md" src="https://github.com/user-attachments/assets/dc6d3e50-6ee4-4afe-ac31-7fdfae32d8b8" />

__Check them on the MongoDBAtlas database__

<img width="900" alt="ndb" src="https://github.com/user-attachments/assets/ba4fa0d8-9edb-46b1-ab04-5b9ea4c1f35c" />

### Conclusion

By following this documentation and leveraging the provided resources, you have created a simple To-Do and deployed it to MERN stack, wrote a frontend application using React.js that communicates with a backend application written using Expressjs. You also created a Mongodb backend for storing tasks in a database.
