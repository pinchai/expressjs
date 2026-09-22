# Express.js Course — Lesson 03
## Request & Response

### Learning Objectives

By the end of this lesson, students should be able to:

- Understand the Express.js Request and Response objects.
- Read route parameters with `req.params`.
- Read query parameters with `req.query`.
- Read request body data with `req.body`.
- Read HTTP headers with `req.headers`.
- Send text responses with `res.send()`.
- Send JSON responses with `res.json()`.
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

Common response methods include:

```javascript
res.send()
res.json()
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

# 17. `res.status()`

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

# 18. Common Status Codes

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

# 19. Combining `res.status()` and `res.json()`

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

# 20. `res.redirect()`

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

# 21. Redirect with a Status Code

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

# 22. Complete Request & Response Example

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

# 23. Understanding the Complete Request

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

# 24. Request Data Summary

| Request Data | Express Property | Example |
|---|---|---|
| Route parameter | `req.params` | `/products/10` |
| Query parameter | `req.query` | `?category=phone` |
| JSON body | `req.body` | `{ "name": "Phone" }` |
| Headers | `req.headers` | `Authorization` |

---

# 25. Response Method Summary

| Method | Purpose |
|---|---|
| `res.send()` | Send a response |
| `res.json()` | Send JSON |
| `res.status()` | Set HTTP status |
| `res.redirect()` | Redirect client |

---

# 26. Practice Exercise

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

# 27. Lab Practice — Product API

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

# 28. Review Questions

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

---

# 29. Key Points

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
