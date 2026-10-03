# Express.js Course — Lesson 03
## Request & Response

### Learning Objectives

By the end of this lesson, students should be able to:

- Understand the Express.js Request and Response objects.
- Read route parameters with `req.params`.
- Read query parameters with `req.query`.
- Read request body data with `req.body`.
- Read HTTP headers with `req.headers`.
- Send text or HTML responses with `res.send()`.
- Send JSON responses with `res.json()`.
- Send files with `res.sendFile()`.
- Download files with `res.download()`.
- Serve static files and directories with `express.static()`.
- Set HTTP status codes with `res.status()`.
- Redirect clients with `res.redirect()`.

---

# 1. Introduction to Request & Response

When a client communicates with an Express.js server, there are two important objects:

```text
Request
   ↓
Client → Server

Response
   ↓
Server → Client
```

In an Express.js route handler, these objects are commonly represented as:

```javascript
(req, res)
```

Example:

```javascript
app.get("/", (req, res) => {
    res.send("Hello Express.js");
});
```

Here:

```text
req
↓
Request from the client

res
↓
Response from the server
```

---

# 2. The Request Object

The `req` object contains information about the HTTP request sent by the client.

For example, a request may contain:

- Route parameters
- Query parameters
- Request body
- HTTP headers
- Request information

Express.js provides properties such as:

```javascript
req.params
req.query
req.body
req.headers
```

---

# 3. `req.params`

`req.params` is used to access **route parameters**.

Example route:

```javascript
app.get("/products/:id", (req, res) => {
    const id = req.params.id;

    res.send(`Product ID: ${id}`);
});
```

Request:

```text
GET /products/10
```

Response:

```text
Product ID: 10
```

The value:

```text
10
```

is available through:

```javascript
req.params.id
```

---

# 4. Multiple Route Parameters

A route can contain multiple parameters.

Example:

```javascript
app.get("/students/:studentId/courses/:courseId", (req, res) => {
    const studentId = req.params.studentId;
    const courseId = req.params.courseId;

    res.send(`Student: ${studentId}, Course: ${courseId}`);
});
```

Request:

```text
GET /students/10/courses/5
```

Response:

```text
Student: 10, Course: 5
```

The values are:

```javascript
req.params.studentId
req.params.courseId
```

---

# 5. `req.query`

`req.query` is used to access **query parameters**.

Example request:

```text
GET /products?category=phone
```

Route:

```javascript
app.get("/products", (req, res) => {
    const category = req.query.category;

    res.send(`Category: ${category}`);
});
```

Response:

```text
Category: phone
```

---

# 6. Multiple Query Parameters

A request can contain multiple query parameters.

Example:

```text
/products?category=phone&sort=price
```

Code:

```javascript
app.get("/products", (req, res) => {
    const category = req.query.category;
    const sort = req.query.sort;

    res.send(`Category: ${category}, Sort: ${sort}`);
});
```

Response:

```text
Category: phone, Sort: price
```

---

# 7. `req.params` vs `req.query`

These two properties are commonly used in APIs.

### Route Parameter

Request:

```text
GET /products/10
```

Route:

```javascript
app.get("/products/:id", (req, res) => {
    console.log(req.params.id);
});
```

Access:

```javascript
req.params.id
```

### Query Parameter

Request:

```text
GET /products?category=phone
```

Route:

```javascript
app.get("/products", (req, res) => {
    console.log(req.query.category);
});
```

Access:

```javascript
req.query.category
```

### Comparison

| Property | Purpose | Example |
|---|---|---|
| `req.params` | Route parameters | `/products/10` |
| `req.query` | Query parameters | `/products?category=phone` |

---

# 8. `req.body`

`req.body` is used to access data sent in the request body.

This is commonly used with:

```text
POST
PUT
PATCH
```

For JSON request bodies, enable Express's JSON middleware:

```javascript
app.use(express.json());
```

Example:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

app.post("/products", (req, res) => {
    const product = req.body;

    res.json(product);
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

A client can send:

```json
{
    "name": "iPhone 17",
    "price": 999
}
```

The server can access:

```javascript
req.body.name
req.body.price
```

Example:

```javascript
app.post("/products", (req, res) => {
    const name = req.body.name;
    const price = req.body.price;

    res.send(`Product: ${name}, Price: ${price}`);
});
```

---

# 9. Why `express.json()` Is Important

If a client sends JSON data:

```json
{
    "name": "Laptop",
    "price": 1200
}
```

Express needs JSON parsing middleware:

```javascript
app.use(express.json());
```

Without the appropriate body-parsing middleware, the JSON request body may not be available through `req.body`.

This middleware will be studied more deeply in the Middleware lesson.

---

# 10. `req.headers`

HTTP requests can contain headers.

Headers provide additional information about a request.

Examples include:

```text
Content-Type
Authorization
User-Agent
Accept
```

Express.js provides headers through:

```javascript
req.headers
```

Example:

```javascript
app.get("/headers", (req, res) => {
    console.log(req.headers);

    res.send("Headers received");
});
```

---

# 11. Reading a Specific Header

You can access a specific header:

```javascript
app.get("/headers", (req, res) => {
    const userAgent = req.headers["user-agent"];

    res.send(userAgent);
});
```

You can also use:

```javascript
req.get("User-Agent")
```

Example:

```javascript
app.get("/headers", (req, res) => {
    const userAgent = req.get("User-Agent");

    res.send(userAgent);
});
```

---

# 12. Authorization Header

Authentication systems often use the `Authorization` header.

Example:

```text
Authorization: Bearer token123
```

You can read it with:

```javascript
app.get("/profile", (req, res) => {
    const authorization = req.headers.authorization;

    res.send(authorization);
});
```

Authentication and JWT will be covered later in the course.

---

# 13. The Response Object

The `res` object is used to send a response back to the client.

Common response methods and middleware include:

```javascript
res.send()
res.json()
res.sendFile()
res.download()
express.static()
res.status()
res.redirect()
```

---

# 14. `res.send()`

`res.send()` sends a response to the client.

Example:

```javascript
app.get("/", (req, res) => {
    res.send("Hello Express.js");
});
```

Response:

```text
Hello Express.js
```

It can also send HTML:

```javascript
app.get("/html", (req, res) => {
    res.send("<h1>Hello Express.js</h1>");
});
```

---

# 15. `res.json()`

`res.json()` sends a JSON response.

Example:

```javascript
app.get("/api/products", (req, res) => {
    res.json({
        id: 1,
        name: "Laptop",
        price: 1200
    });
});
```

Response:

```json
{
    "id": 1,
    "name": "Laptop",
    "price": 1200
}
```

JSON responses are commonly used when building REST APIs.

---

# 16. Returning an Array with `res.json()`

Example:

```javascript
app.get("/api/products", (req, res) => {
    const products = [
        {
            id: 1,
            name: "Laptop",
            price: 1200
        },
        {
            id: 2,
            name: "Phone",
            price: 800
        }
    ];

    res.json(products);
});
```

Response:

```json
[
    {
        "id": 1,
        "name": "Laptop",
        "price": 1200
    },
    {
        "id": 2,
        "name": "Phone",
        "price": 800
    }
]
```

---

# 21. `res.sendFile()`

`res.sendFile()` sends a file from the server to the client.

It is useful when you want to display or return a specific file such as:

- HTML
- PDF
- Image
- Text file
- Video
- Other supported files

Example project structure:

```text
project/
├── app.js
└── files/
    └── report.pdf
```

Example:

```javascript
const path = require("path");

app.get("/report", (req, res) => {
    const filePath = path.join(__dirname, "files", "report.pdf");

    res.sendFile(filePath);
});
```

When the client visits:

```text
GET /report
```

Express sends `report.pdf` to the client.

### Why use `path.join()`?

Using Node.js `path.join()` helps create a file path that works correctly across operating systems.

```javascript
const filePath = path.join(__dirname, "files", "report.pdf");
```

### Sending an HTML file

Example:

```javascript
app.get("/home", (req, res) => {
    const filePath = path.join(__dirname, "public", "index.html");

    res.sendFile(filePath);
});
```

---

# 22. `res.download()`

`res.download()` tells the browser to download a file.

Example:

```javascript
const path = require("path");

app.get("/download-report", (req, res) => {
    const filePath = path.join(__dirname, "files", "report.pdf");

    res.download(filePath);
});
```

When the client visits:

```text
GET /download-report
```

the browser will normally download the file instead of displaying it.

### Custom Download Filename

You can provide a different filename for the downloaded file:

```javascript
app.get("/download-report", (req, res) => {
    const filePath = path.join(__dirname, "files", "report.pdf");

    res.download(filePath, "student-report.pdf");
});
```

The original file is:

```text
report.pdf
```

but the downloaded filename will be:

```text
student-report.pdf
```

### `res.sendFile()` vs `res.download()`

| Method | Purpose |
|---|---|
| `res.sendFile()` | Send a file to the client |
| `res.download()` | Ask the browser to download a file |

For example:

```javascript
res.sendFile(filePath);
```

is useful when the browser can display the file.

```javascript
res.download(filePath);
```

is useful when the user should download the file.

---

# 23. `express.static()`

`express.static()` is built-in Express middleware used to serve static files from a directory.

Static files are files that are sent to the client without server-side processing.

Common static files include:

```text
HTML
CSS
JavaScript
Images
Fonts
PDF files
```

Example project structure:

```text
project/
├── app.js
└── public/
    ├── index.html
    ├── css/
    │   └── style.css
    ├── js/
    │   └── app.js
    └── images/
        └── logo.png
```

Configure the `public` directory:

```javascript
const path = require("path");

app.use(express.static(path.join(__dirname, "public")));
```

Now a file such as:

```text
public/index.html
```

can be requested with:

```text
GET /index.html
```

And:

```text
public/css/style.css
```

can be requested with:

```text
GET /css/style.css
```

### Serving a Directory with a URL Prefix

You can also add a virtual URL prefix:

```javascript
app.use("/static", express.static(path.join(__dirname, "public")));
```

Now:

```text
public/images/logo.png
```

can be accessed through:

```text
/static/images/logo.png
```

The `/static` part is only the URL prefix. It does not need to exist as a physical directory.

### `express.static()` vs `res.sendFile()`

| Feature | `express.static()` | `res.sendFile()` |
|---|---|---|
| Purpose | Serve files from a directory | Send one specific file |
| Common use | CSS, JS, images, HTML | Specific PDF, HTML, image, etc. |
| Route required | No specific route required | Usually used inside a route |
| Example | `app.use(express.static("public"))` | `res.sendFile(filePath)` |

### Complete Static File Example

```javascript
const express = require("express");
const path = require("path");

const app = express();

app.use(express.static(path.join(__dirname, "public")));

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

If the project contains:

```text
public/
└── index.html
```

the browser can request:

```text
http://localhost:3000/index.html
```

---

# 24. Response Method Summary

Express provides several ways to send content to a client:

```text
res.send()
      ↓
Send data

res.json()
      ↓
Send JSON

res.sendFile()
      ↓
Send a specific file

res.download()
      ↓
Download a file

express.static()
      ↓
Serve files from a directory
```

Example:

```javascript
app.get("/text", (req, res) => {
    res.send("Hello Express.js");
});

app.get("/api/product", (req, res) => {
    res.json({
        id: 1,
        name: "Laptop"
    });
});

app.get("/report", (req, res) => {
    res.sendFile(path.join(__dirname, "files", "report.pdf"));
});

app.get("/download", (req, res) => {
    res.download(
        path.join(__dirname, "files", "report.pdf")
    );
});
```

For a directory:

```javascript
app.use(express.static(path.join(__dirname, "public")));
```

---

# 25. `res.status()`

`res.status()` sets the HTTP status code.

Example:

```javascript
app.get("/products", (req, res) => {
    res.status(200).json({
        message: "Product list"
    });
});
```

The response status is:

```text
200 OK
```

---

# 22. Common Status Codes

Some common HTTP status codes are:

| Status | Meaning |
|---:|---|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

We will study HTTP errors in more detail in the Error Handling lesson.

---

# 23. Combining `res.status()` and `res.json()`

These methods can be chained:

```javascript
app.post("/products", (req, res) => {
    res.status(201).json({
        message: "Product created"
    });
});
```

The response is:

```text
Status: 201 Created
```

with JSON:

```json
{
    "message": "Product created"
}
```

---

# 24. `res.redirect()`

`res.redirect()` redirects the client to another URL.

Example:

```javascript
app.get("/old-page", (req, res) => {
    res.redirect("/new-page");
});

app.get("/new-page", (req, res) => {
    res.send("New Page");
});
```

When the client visits:

```text
/old-page
```

Express redirects the client to:

```text
/new-page
```

---

# 25. Redirect with a Status Code

You can specify a redirect status:

```javascript
res.redirect(301, "/new-page");
```

This sends a permanent redirect response.

Another example:

```javascript
res.redirect(302, "/login");
```

---

# 26. Complete Request & Response Example

The following example combines several concepts:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

app.get("/products/:id", (req, res) => {
    const id = req.params.id;
    const category = req.query.category;

    res.status(200).json({
        id: id,
        category: category
    });
});

app.post("/products", (req, res) => {
    const product = req.body;

    res.status(201).json({
        message: "Product created",
        product: product
    });
});

app.get("/headers", (req, res) => {
    const userAgent = req.headers["user-agent"];

    res.json({
        userAgent: userAgent
    });
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

---

# 27. Understanding the Complete Request

Consider:

```text
GET /products/10?category=phone
```

The request contains:

```text
Route parameter
      ↓
10

Query parameter
      ↓
category=phone
```

Express can access them using:

```javascript
req.params.id
req.query.category
```

The response can be:

```javascript
res.status(200).json({
    id: req.params.id,
    category: req.query.category
});
```

---

# 28. Request Data Summary

| Request Data | Express Property | Example |
|---|---|---|
| Route parameter | `req.params` | `/products/10` |
| Query parameter | `req.query` | `?category=phone` |
| JSON body | `req.body` | `{ "name": "Phone" }` |
| Headers | `req.headers` | `Authorization` |

---

# 29. Response Method Summary

| Method / Middleware | Purpose |
|---|---|
| `res.send()` | Send a response |
| `res.json()` | Send JSON |
| `res.sendFile()` | Send a specific file |
| `res.download()` | Download a file |
| `express.static()` | Serve files from a directory |
| `res.status()` | Set HTTP status |
| `res.redirect()` | Redirect client |

---

# 30. Practice Exercise

## Exercise 1 — Route Parameter

Create:

```text
GET /students/:id
```

For:

```text
GET /students/101
```

return:

```json
{
    "studentId": "101"
}
```

---

## Exercise 2 — Query Parameter

Create:

```text
GET /students?gender=male
```

Return:

```json
{
    "gender": "male"
}
```

---

## Exercise 3 — Request Body

Create:

```text
POST /students
```

Accept:

```json
{
    "name": "Dara",
    "age": 20
}
```

Return:

```json
{
    "message": "Student created",
    "student": {
        "name": "Dara",
        "age": 20
    }
}
```

Use:

```javascript
app.use(express.json());
```

---

## Exercise 4 — Status Code

Create:

```text
POST /products
```

Return status:

```text
201
```

and:

```json
{
    "message": "Product created"
}
```

---

## Exercise 5 — Headers

Create:

```text
GET /user-agent
```

Read the `User-Agent` header and return it as JSON.

---

# 30. Practice Exercise — Files and Static Content

## Exercise 6 — Send a File

Create:

```text
GET /report
```

Requirements:

1. Create a `files` directory.
2. Put a PDF file inside it.
3. Use `res.sendFile()` to send the PDF.
4. Use `path.join()` to create the file path.

---

## Exercise 7 — Download a File

Create:

```text
GET /download-report
```

Requirements:

1. Use `res.download()`.
2. Download a PDF from the `files` directory.
3. Set a custom download filename.

---

## Exercise 8 — Static Directory

Create this structure:

```text
public/
├── index.html
├── css/
│   └── style.css
└── images/
    └── logo.png
```

Configure:

```javascript
app.use(express.static("public"));
```

Then test:

```text
/index.html
/css/style.css
/images/logo.png
```

---

## Exercise 9 — Static Directory with Prefix

Configure the `public` directory using:

```javascript
app.use("/static", express.static("public"));
```

Then determine the URL for:

```text
public/images/logo.png
```

---

# 31. Lab Practice — Product API

Build a Product API with:

```text
GET    /api/products
GET    /api/products/:id
POST   /api/products
PUT    /api/products/:id
PATCH  /api/products/:id
DELETE /api/products/:id
```

Requirements:

### GET `/api/products`

Return a JSON array.

### GET `/api/products/:id`

Read:

```javascript
req.params.id
```

and return the product ID.

### POST `/api/products`

Read product information from:

```javascript
req.body
```

and return status:

```text
201
```

### PUT `/api/products/:id`

Read:

```javascript
req.params.id
req.body
```

and return the updated information.

### PATCH `/api/products/:id`

Read:

```javascript
req.params.id
req.body
```

and return the partially updated information.

### DELETE `/api/products/:id`

Read:

```javascript
req.params.id
```

and return a JSON response confirming the deletion.

---

# 32. Review Questions

1. What is the purpose of the `req` object?
2. What is the purpose of the `res` object?
3. What is `req.params`?
4. What is `req.query`?
5. What is `req.body`?
6. Why do we use `express.json()`?
7. What is `req.headers` used for?
8. What does `res.send()` do?
9. What does `res.json()` do?
10. What does `res.status()` do?
11. What does `res.redirect()` do?
12. What status code normally represents a successful request?
13. What status code is commonly used after creating a resource?
14. What is the difference between `req.params` and `req.query`?
15. How do you return a JSON response with status code `201`?
16. How do you read a product ID from `/products/:id`?
17. How do you read `category` from `/products?category=phone`?
18. How do you read a JSON product sent in a POST request?
19. What does `res.sendFile()` do?
20. What is the difference between `res.sendFile()` and `res.download()`?
21. What does `res.download()` do?
22. What is `express.static()` used for?
23. What is the difference between `express.static()` and `res.sendFile()`?
24. How do you serve a `public` directory using `express.static()`?
25. How can you add a URL prefix when using `express.static()`?
19. What does `res.sendFile()` do?
20. What is the difference between `res.sendFile()` and `res.download()`?
21. What does `res.download()` do?
22. What is `express.static()` used for?
23. What is the difference between `express.static()` and `res.sendFile()`?
24. How do you serve a `public` directory using `express.static()`?
25. How can you add a URL prefix when using `express.static()`?

---

# 33. Key Points

Remember:

```text
REQUEST
   │
   ├── req.params
   │      └── Route parameters
   │
   ├── req.query
   │      └── Query parameters
   │
   ├── req.body
   │      └── Request body
   │
   └── req.headers
          └── HTTP headers
```

Response:

```text
RESPONSE
   │
   ├── res.send()
   │      └── Send response
   │
   ├── res.json()
   │      └── Send JSON
   │
   ├── res.status()
   │      └── Set status code
   │
   └── res.redirect()
          └── Redirect client
```

---

# Next Lesson

## Lesson 04 — Middleware

Topics:

- What is middleware?
- How middleware works
- Application-level middleware
- Router-level middleware
- Built-in middleware
- Custom middleware
- Error-handling middleware
