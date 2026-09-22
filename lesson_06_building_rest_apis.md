# Express.js Course — Lesson 06
## Building REST APIs

### Learning Objectives

By the end of this lesson, students should be able to:

- Explain what a REST API is.
- Understand the relationship between resources and HTTP methods.
- Design REST-style API endpoints.
- Use GET, POST, PUT, and DELETE correctly in a Product API.
- Return JSON responses.
- Use appropriate HTTP status codes.
- Implement basic CRUD operations.
- Build a complete Product REST API using Express.js.

---

# 1. What is a REST API?

A **REST API** is an API designed around resources and standard HTTP methods.

REST stands for:

```text
Representational State Transfer
```

A REST-style API commonly uses:

```text
URL + HTTP Method
```

to describe an operation on a resource.

For example, if our resource is:

```text
products
```

we can use:

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
DELETE  /api/products/:id
```

---

# 2. What is a Resource?

A resource is something that an API manages.

Examples:

```text
Products
Users
Categories
Orders
Students
```

For this lesson, our main resource is:

```text
Product
```

The collection URL is:

```text
/api/products
```

A specific product is:

```text
/api/products/:id
```

Example:

```text
/api/products/10
```

---

# 3. REST API and HTTP Methods

REST APIs commonly use HTTP methods to describe actions.

| HTTP Method | Common Purpose |
|---|---|
| GET | Retrieve data |
| POST | Create data |
| PUT | Update data |
| PATCH | Partially update data |
| DELETE | Delete data |

For this lesson, the core Product REST API is:

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
DELETE  /api/products/:id
```

---

# 4. REST API CRUD

CRUD represents four common data operations:

```text
C → Create
R → Read
U → Update
D → Delete
```

Mapping CRUD to HTTP methods:

| CRUD | HTTP Method | Endpoint |
|---|---|---|
| Create | POST | `/api/products` |
| Read | GET | `/api/products` |
| Read One | GET | `/api/products/:id` |
| Update | PUT | `/api/products/:id` |
| Delete | DELETE | `/api/products/:id` |

This CRUD pattern will be used throughout the course.

---

# 5. REST API URL Design

A REST-style API should use resource names.

Recommended:

```text
/api/products
/api/users
/api/orders
/api/categories
```

Instead of action-oriented URLs such as:

```text
/api/getProducts
/api/createProduct
/api/deleteProduct
```

The HTTP method describes the operation.

For example:

```text
GET /api/products
```

means retrieve products.

```text
POST /api/products
```

means create a product.

---

# 6. Create the Project

Create a project:

```bash
mkdir product-rest-api
cd product-rest-api
```

Initialize Node.js:

```bash
npm init -y
```

Install Express:

```bash
npm install express
```

Project structure:

```text
product-rest-api/
│
├── node_modules/
├── package.json
├── package-lock.json
└── server.js
```

---

# 7. Create the Express Application

Create:

```text
server.js
```

Add:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

The important middleware is:

```javascript
app.use(express.json());
```

It allows the application to read JSON request bodies.

---

# 8. Product Data

For this lesson, we will use an in-memory array instead of a database.

Example:

```javascript
let products = [
    {
        id: 1,
        name: "Laptop",
        price: 1200
    },
    {
        id: 2,
        name: "Phone",
        price: 800
    },
    {
        id: 3,
        name: "Keyboard",
        price: 80
    }
];
```

This is for learning the REST API concepts.

Database integration will be covered in Lesson 08.

---

# 9. GET — Get All Products

The first endpoint is:

```text
GET /api/products
```

Code:

```javascript
app.get("/api/products", (req, res) => {
    res.json(products);
});
```

When the client requests:

```text
GET /api/products
```

the server returns the product array.

Example response:

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

# 10. GET — Get One Product

The second endpoint is:

```text
GET /api/products/:id
```

Code:

```javascript
app.get("/api/products/:id", (req, res) => {
    const id = Number(req.params.id);

    const product = products.find(product => product.id === id);

    if (!product) {
        return res.status(404).json({
            message: "Product not found"
        });
    }

    res.json(product);
});
```

Request:

```text
GET /api/products/2
```

Response:

```json
{
    "id": 2,
    "name": "Phone",
    "price": 800
}
```

---

# 11. Why Convert the ID to a Number?

Route parameters are received as strings.

For example:

```javascript
req.params.id
```

may contain:

```text
"2"
```

But our product IDs are numbers:

```javascript
2
```

Therefore, we convert the parameter:

```javascript
const id = Number(req.params.id);
```

Now we can compare it with:

```javascript
product.id
```

---

# 12. Product Not Found

If the product does not exist:

```text
GET /api/products/999
```

the API should return an appropriate error response.

Example:

```javascript
return res.status(404).json({
    message: "Product not found"
});
```

Response:

```json
{
    "message": "Product not found"
}
```

Status:

```text
404 Not Found
```

---

# 13. POST — Create a Product

The endpoint is:

```text
POST /api/products
```

The client sends product data in the request body.

Example:

```json
{
    "name": "Monitor",
    "price": 300
}
```

Code:

```javascript
app.post("/api/products", (req, res) => {
    const { name, price } = req.body;

    const product = {
        id: products.length + 1,
        name: name,
        price: price
    };

    products.push(product);

    res.status(201).json(product);
});
```

---

# 14. Understanding `201 Created`

When a new resource is successfully created, the API commonly returns:

```text
201 Created
```

Example:

```javascript
res.status(201).json(product);
```

The response contains the newly created product.

---

# 15. PUT — Update a Product

The endpoint is:

```text
PUT /api/products/:id
```

Example:

```text
PUT /api/products/2
```

Request body:

```json
{
    "name": "iPhone",
    "price": 999
}
```

Code:

```javascript
app.put("/api/products/:id", (req, res) => {
    const id = Number(req.params.id);

    const productIndex = products.findIndex(
        product => product.id === id
    );

    if (productIndex === -1) {
        return res.status(404).json({
            message: "Product not found"
        });
    }

    const { name, price } = req.body;

    products[productIndex] = {
        id: id,
        name: name,
        price: price
    };

    res.json(products[productIndex]);
});
```

---

# 16. DELETE — Delete a Product

The endpoint is:

```text
DELETE /api/products/:id
```

Example:

```text
DELETE /api/products/3
```

Code:

```javascript
app.delete("/api/products/:id", (req, res) => {
    const id = Number(req.params.id);

    const productIndex = products.findIndex(
        product => product.id === id
    );

    if (productIndex === -1) {
        return res.status(404).json({
            message: "Product not found"
        });
    }

    const deletedProduct = products.splice(productIndex, 1);

    res.json({
        message: "Product deleted",
        product: deletedProduct[0]
    });
});
```

---

# 17. Complete Product REST API

Here is the complete example:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

let products = [
    {
        id: 1,
        name: "Laptop",
        price: 1200
    },
    {
        id: 2,
        name: "Phone",
        price: 800
    },
    {
        id: 3,
        name: "Keyboard",
        price: 80
    }
];

// GET all products
app.get("/api/products", (req, res) => {
    res.json(products);
});

// GET one product
app.get("/api/products/:id", (req, res) => {
    const id = Number(req.params.id);

    const product = products.find(
        product => product.id === id
    );

    if (!product) {
        return res.status(404).json({
            message: "Product not found"
        });
    }

    res.json(product);
});

// CREATE product
app.post("/api/products", (req, res) => {
    const { name, price } = req.body;

    const product = {
        id: products.length + 1,
        name: name,
        price: price
    };

    products.push(product);

    res.status(201).json(product);
});

// UPDATE product
app.put("/api/products/:id", (req, res) => {
    const id = Number(req.params.id);

    const productIndex = products.findIndex(
        product => product.id === id
    );

    if (productIndex === -1) {
        return res.status(404).json({
            message: "Product not found"
        });
    }

    const { name, price } = req.body;

    products[productIndex] = {
        id: id,
        name: name,
        price: price
    };

    res.json(products[productIndex]);
});

// DELETE product
app.delete("/api/products/:id", (req, res) => {
    const id = Number(req.params.id);

    const productIndex = products.findIndex(
        product => product.id === id
    );

    if (productIndex === -1) {
        return res.status(404).json({
            message: "Product not found"
        });
    }

    const deletedProduct = products.splice(productIndex, 1);

    res.json({
        message: "Product deleted",
        product: deletedProduct[0]
    });
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

---

# 18. API Endpoint Summary

Our Product REST API now has:

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/products` | Get all products |
| GET | `/api/products/:id` | Get one product |
| POST | `/api/products` | Create product |
| PUT | `/api/products/:id` | Update product |
| DELETE | `/api/products/:id` | Delete product |

---

# 19. Testing the API

Start the server:

```bash
node server.js
```

You should see:

```text
Server running on http://localhost:3000
```

## Test GET All

```text
GET http://localhost:3000/api/products
```

## Test GET One

```text
GET http://localhost:3000/api/products/1
```

## Test POST

```text
POST http://localhost:3000/api/products
```

Body:

```json
{
    "name": "Monitor",
    "price": 300
}
```

## Test PUT

```text
PUT http://localhost:3000/api/products/1
```

Body:

```json
{
    "name": "Gaming Laptop",
    "price": 1500
}
```

## Test DELETE

```text
DELETE http://localhost:3000/api/products/1
```

---

# 20. HTTP Status Codes in the Product API

The API can use:

### 200 OK

Successful GET, PUT, or DELETE response.

```javascript
res.status(200).json(data);
```

In many Express responses, `200` is the default successful status, so this is also valid:

```javascript
res.json(data);
```

### 201 Created

Successful POST:

```javascript
res.status(201).json(product);
```

### 404 Not Found

Product does not exist:

```javascript
res.status(404).json({
    message: "Product not found"
});
```

---

# 21. REST API Request Flow

For:

```text
POST /api/products
```

the flow is:

```text
Client
   ↓
POST /api/products
   ↓
Express
   ↓
Read req.body
   ↓
Create Product
   ↓
res.status(201)
   ↓
JSON Response
   ↓
Client
```

For:

```text
GET /api/products/10
```

the flow is:

```text
Client
   ↓
GET /api/products/10
   ↓
Express
   ↓
req.params.id
   ↓
Find Product
   ↓
res.json()
   ↓
Client
```

---

# 22. Why Use JSON?

REST APIs commonly exchange data using JSON.

Example request:

```json
{
    "name": "Laptop",
    "price": 1200
}
```

Example response:

```json
{
    "id": 1,
    "name": "Laptop",
    "price": 1200
}
```

JSON is commonly used because it is easy for applications to send, receive, and process.

---

# 23. Current Project Limitation

Our products are stored in:

```javascript
let products = [];
```

This means the data is stored only in application memory.

If the server restarts:

```text
Server stops
    ↓
Memory is cleared
    ↓
Product data is lost
```

This is acceptable for learning REST API fundamentals.

In **Lesson 08 — Database Integration**, we will connect Express.js to databases.

---

# 24. Practice Exercise

## Exercise 1 — Create a Student REST API

Create:

```text
GET     /api/students
GET     /api/students/:id
POST    /api/students
PUT     /api/students/:id
DELETE  /api/students/:id
```

Use an in-memory array:

```javascript
let students = [
    {
        id: 1,
        name: "Dara",
        age: 20
    },
    {
        id: 2,
        name: "Sokha",
        age: 21
    }
];
```

---

## Exercise 2 — Product Not Found

Make sure:

```text
GET /api/products/999
```

returns:

```json
{
    "message": "Product not found"
}
```

with status:

```text
404
```

---

## Exercise 3 — Create Product

Create:

```text
POST /api/products
```

with:

```json
{
    "name": "Mouse",
    "price": 25
}
```

Return:

```text
201 Created
```

---

# 25. Lab Practice — Complete Product REST API

Build a complete Product REST API.

Requirements:

### Data

Each product must contain:

```text
id
name
price
```

### Endpoints

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
DELETE  /api/products/:id
```

### Requirements

1. Use Express.js.
2. Use `express.json()`.
3. Store products in an array.
4. Return JSON responses.
5. Use route parameters for product IDs.
6. Use status `201` when creating a product.
7. Use status `404` when a product does not exist.
8. Test all CRUD operations using an API testing tool.

---

# 26. Review Questions

1. What is a REST API?
2. What does REST stand for?
3. What is a resource in a REST API?
4. What is CRUD?
5. What does GET do?
6. What does POST do?
7. What does PUT do?
8. What does DELETE do?
9. What is the difference between `/api/products` and `/api/products/:id`?
10. Why should REST API URLs normally represent resources?
11. What is the purpose of `express.json()`?
12. Why do we convert `req.params.id` to a number in this example?
13. What status code is commonly returned when a resource is created?
14. What status code is used when a product cannot be found?
15. Why is an in-memory array not suitable for permanent production data?
16. Create a Product REST API with all CRUD operations.
17. Explain the request flow for `POST /api/products`.
18. Explain the request flow for `GET /api/products/:id`.

---

# 27. Key Points

Remember:

```text
REST API
   ↓
Resource + HTTP Method
```

Product resource:

```text
/api/products
```

CRUD:

```text
CREATE
   ↓
POST

READ
   ↓
GET

UPDATE
   ↓
PUT

DELETE
   ↓
DELETE
```

Product API:

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
DELETE  /api/products/:id
```

Common responses:

```text
200 OK
201 Created
404 Not Found
```

---

# Next Lesson

## Lesson 07 — MVC Architecture

Topics:

```text
Routes
   ↓
Controllers
   ↓
Services
   ↓
Models
   ↓
Database
```

We will learn:

- Routes
- Controllers
- Services
- Models
- Separation of concerns
- Project structure
