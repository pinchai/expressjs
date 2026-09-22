# Express.js Course — Lesson 01
## Introduction to Express.js

### Learning Objectives

By the end of this lesson, students should be able to:

- Explain what Express.js is.
- Explain the relationship between Node.js and Express.js.
- Install Express.js in a Node.js project.
- Create a basic Express.js server.
- Understand `app.listen()`.
- Understand the basic Request and Response objects.

---

## 1. What is Express.js?

**Express.js** is a web framework for **Node.js**.

It provides tools and features that make it easier to build:

- Web applications
- REST APIs
- Backend services
- HTTP servers

Instead of handling every HTTP request directly with Node.js's built-in modules, Express.js provides a simpler programming interface for common server-side tasks.

### Simple idea

```text
Node.js
   ↓
Express.js
   ↓
Web Server / REST API
```

Express.js runs on top of Node.js and helps developers organize server-side applications.

---

## 2. Node.js vs Express.js

### Node.js

Node.js is a **JavaScript runtime environment** that allows JavaScript to run outside the browser.

Example:

```javascript
console.log("Hello Node.js");
```

Node.js also provides built-in modules such as:

```javascript
http
fs
path
url
```

### Express.js

Express.js is a **web framework for Node.js**.

It provides convenient features for:

- Routing
- Request handling
- Response handling
- Middleware
- REST APIs
- Error handling

### Comparison

| Node.js | Express.js |
|---|---|
| JavaScript runtime | Web framework |
| Runs JavaScript outside browser | Runs on Node.js |
| Provides built-in modules | Provides higher-level web features |
| Can create HTTP servers | Makes HTTP server development easier |
| Lower-level API | Higher-level API |

### Important

Express.js does **not** replace Node.js.

Instead:

```text
Node.js
   +
Express.js
   =
Express Web Application
```

---

# 3. Create an Express.js Project

First, create a project directory.

```bash
mkdir express-lesson-01
cd express-lesson-01
```

Initialize a Node.js project:

```bash
npm init -y
```

This creates:

```text
package.json
```

---

# 4. Install Express.js

Install Express.js:

```bash
npm install express
```

After installation, the project will contain:

```text
express-lesson-01/
│
├── node_modules/
├── package-lock.json
└── package.json
```

The `package.json` file will contain Express.js as a dependency.

Example:

```json
{
  "name": "express-lesson-01",
  "version": "1.0.0",
  "dependencies": {
    "express": "^5.0.0"
  }
}
```

The exact Express version may be different depending on when the package is installed.

---

# 5. Create Your First Express Server

Create a file:

```text
server.js
```

Add:

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("Hello Express.js!");
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

---

# 6. Understanding the Code

## Import Express

```javascript
const express = require("express");
```

This loads the Express.js package.

---

## Create the Express Application

```javascript
const app = express();
```

`express()` creates an Express application.

The `app` object will be used to configure our server.

For example:

```javascript
app.get(...)
app.post(...)
app.put(...)
app.delete(...)
app.listen(...)
```

---

# 7. Understanding `app.get()`

This code creates a GET route:

```javascript
app.get("/", (req, res) => {
    res.send("Hello Express.js!");
});
```

The first argument is the URL path:

```text
/
```

The second argument is a callback function.

```javascript
(req, res) => {
    res.send("Hello Express.js!");
}
```

The callback receives two important objects:

```text
req → Request
res → Response
```

---

# 8. Understanding Request

The `req` object represents the **HTTP request** sent by the client.

For example, a client may send:

```text
GET /
```

The request can contain information such as:

- URL
- Parameters
- Query strings
- Headers
- Request body

We will study these in more detail in later lessons.

---

# 9. Understanding Response

The `res` object represents the **HTTP response** that the server sends back to the client.

Example:

```javascript
res.send("Hello Express.js!");
```

The server sends:

```text
Hello Express.js!
```

back to the client.

---

# 10. Understanding `app.listen()`

The following code starts the server:

```javascript
app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

The number:

```text
3000
```

is the port number.

After starting the server, open:

```text
http://localhost:3000
```

The browser should display:

```text
Hello Express.js!
```

### How it works

```text
Browser
   │
   │ GET /
   ↓
Express Server
   │
   │ res.send()
   ↓
Browser
```

---

# 11. Run the Express Server

Run:

```bash
node server.js
```

You should see:

```text
Server running on http://localhost:3000
```

Then open the URL in your browser.

---

# 12. Add More Routes

We can create another route:

```javascript
app.get("/about", (req, res) => {
    res.send("About Express.js");
});
```

The complete example:

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("Home Page");
});

app.get("/about", (req, res) => {
    res.send("About Page");
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

Now we have:

```text
GET /
GET /about
```

---

# 13. Request and Response Flow

When a user visits:

```text
http://localhost:3000/about
```

the following happens:

```text
Client
   │
   │ GET /about
   ↓
Express Application
   │
   │ Find matching route
   ↓
app.get("/about", ...)
   │
   │ res.send()
   ↓
Client
```

---

# 14. Basic Express Project Structure

For this lesson, a simple structure is enough:

```text
express-lesson-01/
│
├── node_modules/
├── package.json
├── package-lock.json
└── server.js
```

As the project becomes larger, we will introduce:

```text
routes/
controllers/
services/
models/
middleware/
config/
```

These structures will be covered in later lessons.

---

# 15. Practice Exercise

## Exercise 1 — Create an Express Server

Create an Express.js project named:

```text
student-api
```

Requirements:

1. Install Express.js.
2. Create `server.js`.
3. Start the server on port `3000`.
4. Create a GET route:

```text
GET /
```

Response:

```text
Welcome to Student API
```

---

## Exercise 2 — Create Multiple Routes

Create these routes:

```text
GET /
GET /about
GET /contact
```

Expected responses:

```text
Welcome to Student API
About Student API
Contact Student API
```

---

## Exercise 3 — Server Information

Create a route:

```text
GET /info
```

Return:

```text
Express.js Server
Port: 3000
Status: Running
```

---

# 16. Quick Review

### Question 1

What is Express.js?

### Question 2

What is Node.js?

### Question 3

What is the relationship between Node.js and Express.js?

### Question 4

What command is used to install Express.js?

### Question 5

What does this code do?

```javascript
const app = express();
```

### Question 6

What is the purpose of `app.get()`?

### Question 7

What are `req` and `res`?

### Question 8

What does `res.send()` do?

### Question 9

What is the purpose of `app.listen()`?

### Question 10

What port is used in this example?

```javascript
app.listen(3000);
```

---

# 17. Key Points

Remember these concepts:

```text
Node.js
   ↓
JavaScript runtime

Express.js
   ↓
Web framework for Node.js

express()
   ↓
Creates Express application

app.get()
   ↓
Creates GET route

req
   ↓
HTTP Request

res
   ↓
HTTP Response

res.send()
   ↓
Sends response

app.listen()
   ↓
Starts the server
```

---

# Lab Practice

Build a simple **Student API Server**.

Requirements:

```text
GET /
GET /students
GET /about
```

Use the following responses:

```text
GET /
Welcome to Student API

GET /students
Student List

GET /about
Student Management API
```

Start the server with:

```bash
node server.js
```

Test each route using a web browser or an API testing tool.

---

## Next Lesson

**Lesson 02 — Express.js Routing**

Topics:

- What is routing?
- GET routes
- POST routes
- PUT routes
- PATCH routes
- DELETE routes
- Route parameters
- Query parameters
