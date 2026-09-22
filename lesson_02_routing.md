# Express.js Course — Lesson 02
## Express.js Routing

### Learning Objectives

By the end of this lesson, students should be able to:

- Explain what routing means in Express.js.
- Create GET, POST, PUT, PATCH, and DELETE routes.
- Understand route paths.
- Use route parameters.
- Use query parameters.
- Understand the difference between route parameters and query parameters.
- Build a simple REST-style route structure.

---

# 1. What is Routing?

**Routing** is the process of defining how an application responds to a client's request for a particular URL and HTTP method.

For example:

```text
GET /products
```

can be connected to a function that returns a list of products.

Another request:

```text
GET /products/10
```

can return one product.

In Express.js, routing is commonly written using:

```javascript
app.get()
app.post()
app.put()
app.patch()
app.delete()
```

---

# 2. Basic Route Structure

The basic Express.js route structure is:

```javascript
app.METHOD(PATH, HANDLER);
```

Example:

```javascript
app.get("/", (req, res) => {
    res.send("Home Page");
});
```

There are three important parts:

```text
app.get
   ↓
HTTP Method

"/"
   ↓
Route Path

(req, res) => { ... }
   ↓
Route Handler
```

---

# 3. GET Route

The `GET` method is commonly used to **retrieve data**.

Example:

```javascript
app.get("/products", (req, res) => {
    res.send("Product List");
});
```

When the client requests:

```text
GET /products
```

the server responds:

```text
Product List
```

### Another example

```javascript
app.get("/students", (req, res) => {
    res.send("Student List");
});
```

Request:

```text
GET /students
```

Response:

```text
Student List
```

---

# 4. POST Route

The `POST` method is commonly used to **create new data**.

Example:

```javascript
app.post("/products", (req, res) => {
    res.send("Product Created");
});
```

Request:

```text
POST /products
```

Response:

```text
Product Created
```

A POST request can contain data in the request body.

We will study `req.body` in more detail in Lesson 03.

---

# 5. PUT Route

The `PUT` method is commonly used to **update an existing resource**.

Example:

```javascript
app.put("/products/:id", (req, res) => {
    res.send("Product Updated");
});
```

Request:

```text
PUT /products/10
```

Here:

```text
10
```

is the product ID.

---

# 6. PATCH Route

The `PATCH` method is commonly used to **partially update an existing resource**.

Example:

```javascript
app.patch("/products/:id", (req, res) => {
    res.send("Product Partially Updated");
});
```

Request:

```text
PATCH /products/10
```

For example, if a product has:

```text
name
price
category
stock
```

a PATCH request could update only:

```text
price
```

instead of replacing the entire product.

---

# 7. DELETE Route

The `DELETE` method is commonly used to **delete a resource**.

Example:

```javascript
app.delete("/products/:id", (req, res) => {
    res.send("Product Deleted");
});
```

Request:

```text
DELETE /products/10
```

Response:

```text
Product Deleted
```

---

# 8. HTTP Methods Summary

| Method | Common Purpose | Example |
|---|---|---|
| GET | Retrieve data | `GET /products` |
| POST | Create data | `POST /products` |
| PUT | Update a resource | `PUT /products/10` |
| PATCH | Partially update | `PATCH /products/10` |
| DELETE | Delete data | `DELETE /products/10` |

These methods will become important when we build REST APIs.

---

# 9. Create a Complete Routing Example

Create:

```text
server.js
```

Example:

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("Home Page");
});

app.get("/products", (req, res) => {
    res.send("Product List");
});

app.post("/products", (req, res) => {
    res.send("Product Created");
});

app.put("/products/:id", (req, res) => {
    res.send("Product Updated");
});

app.patch("/products/:id", (req, res) => {
    res.send("Product Partially Updated");
});

app.delete("/products/:id", (req, res) => {
    res.send("Product Deleted");
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

---

# 10. Route Parameters

A **route parameter** is a dynamic value included in the URL path.

Example:

```javascript
app.get("/products/:id", (req, res) => {
    res.send(`Product ID: ${req.params.id}`);
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

Request:

```text
GET /products/25
```

Response:

```text
Product ID: 25
```

The route:

```text
/products/:id
```

matches different IDs.

---

# 11. Accessing Route Parameters

Express.js provides route parameters through:

```javascript
req.params
```

Example:

```javascript
app.get("/students/:id", (req, res) => {
    const id = req.params.id;

    res.send(`Student ID: ${id}`);
});
```

Request:

```text
GET /students/100
```

Response:

```text
Student ID: 100
```

---

# 12. Multiple Route Parameters

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

---

# 13. Query Parameters

A **query parameter** is additional information included after `?` in the URL.

Example:

```text
/products?category=phone
```

The query parameter is:

```text
category=phone
```

Express.js provides query parameters through:

```javascript
req.query
```

Example:

```javascript
app.get("/products", (req, res) => {
    const category = req.query.category;

    res.send(`Category: ${category}`);
});
```

Request:

```text
GET /products?category=phone
```

Response:

```text
Category: phone
```

---

# 14. Multiple Query Parameters

A URL can contain multiple query parameters.

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

# 15. Route Parameter vs Query Parameter

These two concepts are important.

### Route Parameter

URL:

```text
/products/10
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

URL:

```text
/products?category=phone
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

| Route Parameter | Query Parameter |
|---|---|
| Part of URL path | Added after `?` |
| Often identifies a specific resource | Often used for filtering/options |
| `req.params` | `req.query` |
| `/products/10` | `/products?category=phone` |

---

# 16. Practical Product API Example

A product API might use routes like:

```text
GET     /api/products
GET     /api/products/10
POST    /api/products
PUT     /api/products/10
PATCH   /api/products/10
DELETE  /api/products/10
```

Filtering can use query parameters:

```text
GET /api/products?category=phone
```

Sorting:

```text
GET /api/products?sort=price
```

Searching:

```text
GET /api/products?search=iphone
```

Pagination:

```text
GET /api/products?page=1&limit=10
```

These patterns will be used later when building the complete Product REST API.

---

# 17. Testing Routes

You can test GET routes using a browser.

Example:

```text
http://localhost:3000/products
```

For POST, PUT, PATCH, and DELETE requests, use an API testing tool such as Postman, Insomnia, or another HTTP client.

Example:

```text
POST http://localhost:3000/products
```

---

# 18. Route Order

Express.js processes routes in the order they are registered.

Example:

```javascript
app.get("/products/:id", (req, res) => {
    res.send(`Product ${req.params.id}`);
});

app.get("/products/new", (req, res) => {
    res.send("New Product");
});
```

Depending on the route definitions and matching behavior, a dynamic route can match a path intended for a specific route.

For this reason, students should pay attention to route order and specific routes when designing an application.

A clear approach is:

```javascript
app.get("/products/new", (req, res) => {
    res.send("New Product");
});

app.get("/products/:id", (req, res) => {
    res.send(`Product ${req.params.id}`);
});
```

---

# 19. REST-Style Resource Naming

When designing APIs, use resource-oriented URLs.

Prefer:

```text
/products
/students
/orders
/categories
```

instead of action-oriented URLs such as:

```text
/getProducts
/createProduct
/deleteProduct
```

The HTTP method describes the operation:

```text
GET    /products
POST   /products
PUT    /products/10
DELETE /products/10
```

This style will be important in Lesson 06 when we build a complete REST API.

---

# 20. Practice Exercise

## Exercise 1 — Student Routes

Create an Express application with:

```text
GET /students
GET /students/:id
POST /students
PUT /students/:id
PATCH /students/:id
DELETE /students/:id
```

For now, return simple text responses.

Example:

```javascript
app.get("/students", (req, res) => {
    res.send("Student List");
});
```

---

## Exercise 2 — Product Route Parameter

Create:

```text
GET /products/:id
```

For:

```text
GET /products/25
```

return:

```text
Product ID: 25
```

---

## Exercise 3 — Product Filtering

Create:

```text
GET /products?category=phone
```

Return:

```text
Category: phone
```

---

## Exercise 4 — Multiple Query Parameters

Create:

```text
GET /products?category=phone&sort=price
```

Return:

```text
Category: phone
Sort: price
```

---

# 21. Lab Practice — Simple Product API Routes

Build a small Express.js application with these routes:

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
PATCH   /api/products/:id
DELETE  /api/products/:id
```

Also support:

```text
GET /api/products?category=phone
GET /api/products?search=iphone
GET /api/products?sort=price
```

At this stage, you do **not** need a database.

Use simple text or JSON responses.

Example:

```javascript
app.get("/api/products", (req, res) => {
    res.json({
        message: "Product List"
    });
});
```

---

# 22. Review Questions

1. What is routing in Express.js?
2. What is the purpose of `app.get()`?
3. What is the purpose of `app.post()`?
4. What is the difference between PUT and PATCH?
5. What is the purpose of DELETE?
6. What is a route parameter?
7. How do you access route parameters in Express.js?
8. What is a query parameter?
9. How do you access query parameters?
10. What is the difference between `req.params` and `req.query`?
11. What does `/products/:id` mean?
12. What does `/products?category=phone` mean?
13. Why are HTTP methods important in REST APIs?
14. Why should API URLs generally represent resources?
15. Create the routes needed for a basic Product CRUD API.

---

# 23. Key Points

Remember:

```text
Routing
   ↓
Connect HTTP requests to handlers

GET
   ↓
Retrieve

POST
   ↓
Create

PUT
   ↓
Update

PATCH
   ↓
Partially update

DELETE
   ↓
Delete
```

Route parameters:

```text
/products/:id
        ↓
req.params.id
```

Query parameters:

```text
/products?category=phone
        ↓
req.query.category
```

REST-style Product API:

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
PATCH   /api/products/:id
DELETE  /api/products/:id
```

---

# Next Lesson

## Lesson 03 — Request & Response

Topics:

- `req.params`
- `req.query`
- `req.body`
- `req.headers`
- `res.send()`
- `res.json()`
- `res.status()`
- `res.redirect()`
