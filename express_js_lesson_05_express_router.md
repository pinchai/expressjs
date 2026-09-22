# Express.js Course — Lesson 05
## Express Router

### Learning Objectives

By the end of this lesson, students should be able to:

- Explain what `express.Router()` is.
- Understand why routers are useful in Express.js applications.
- Create separate route files.
- Organize routes by resource or feature.
- Create API route modules.
- Connect routers to the main Express application.
- Use router-level middleware.
- Build a clean Product API structure.

---

# 1. What is Express Router?

As an Express.js application grows, putting every route inside `server.js` can make the project difficult to maintain.

For example:

```javascript
app.get("/products", ...);
app.post("/products", ...);
app.put("/products/:id", ...);
app.delete("/products/:id", ...);

app.get("/users", ...);
app.post("/users", ...);
app.put("/users/:id", ...);
app.delete("/users/:id", ...);
```

A better approach is to separate routes into different files.

Express.js provides:

```javascript
express.Router()
```

A Router allows us to create a group of related routes.

---

# 2. Why Use `express.Router()`?

Without routers:

```text
server.js
 ├── Product routes
 ├── User routes
 ├── Category routes
 ├── Order routes
 └── Authentication routes
```

As the application grows, `server.js` becomes large.

With routers:

```text
server.js
    │
    ├── productRoutes
    ├── userRoutes
    ├── categoryRoutes
    └── orderRoutes
```

Each resource can have its own route module.

This improves:

- Organization
- Readability
- Maintainability
- Reusability
- Team development

---

# 3. Creating a Router

First import Express:

```javascript
const express = require("express");
```

Create a router:

```javascript
const router = express.Router();
```

Then define routes on the router:

```javascript
router.get("/", (req, res) => {
    res.send("Product List");
});
```

Export the router:

```javascript
module.exports = router;
```

---

# 4. Create a Product Route File

Create this project structure:

```text
express-router/
│
├── node_modules/
├── package.json
├── server.js
└── routes/
    └── product.routes.js
```

The file:

```text
product.routes.js
```

will contain Product routes.

---

# 5. Product Router

Create:

```text
routes/product.routes.js
```

Add:

```javascript
const express = require("express");

const router = express.Router();

router.get("/", (req, res) => {
    res.json({
        message: "Product List"
    });
});

router.get("/:id", (req, res) => {
    res.json({
        message: "Product Detail",
        id: req.params.id
    });
});

router.post("/", (req, res) => {
    res.json({
        message: "Product Created"
    });
});

router.put("/:id", (req, res) => {
    res.json({
        message: "Product Updated",
        id: req.params.id
    });
});

router.delete("/:id", (req, res) => {
    res.json({
        message: "Product Deleted",
        id: req.params.id
    });
});

module.exports = router;
```

---

# 6. Connect the Router to `server.js`

Now create:

```text
server.js
```

Add:

```javascript
const express = require("express");

const app = express();

const productRoutes = require("./routes/product.routes");

app.use("/api/products", productRoutes);

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

The important line is:

```javascript
app.use("/api/products", productRoutes);
```

This connects the Product Router to the main application.

---

# 7. Understanding Route Prefixes

Inside the router we have:

```javascript
router.get("/", ...);
```

But in `server.js` we have:

```javascript
app.use("/api/products", productRoutes);
```

Therefore:

```text
/api/products
```

is the base path.

The final route becomes:

```text
GET /api/products
```

Similarly:

```javascript
router.get("/:id", ...);
```

becomes:

```text
GET /api/products/:id
```

---

# 8. Complete Product Routes

Our Product Router now provides:

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
DELETE  /api/products/:id
```

This follows the Product REST API structure from the course roadmap.

---

# 9. Why the Router Does Not Need the Full URL

Inside:

```text
product.routes.js
```

we write:

```javascript
router.get("/", ...);
```

not:

```javascript
router.get("/api/products", ...);
```

because the prefix is defined in:

```javascript
app.use("/api/products", productRoutes);
```

This separation makes route modules easier to reuse and maintain.

---

# 10. Adding JSON Middleware

If Product routes receive JSON request bodies, add:

```javascript
app.use(express.json());
```

before the routes.

Example:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

const productRoutes = require("./routes/product.routes");

app.use("/api/products", productRoutes);

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

Now POST and PUT requests can receive JSON data.

---

# 11. Reading Request Body in a Router

Inside:

```text
product.routes.js
```

we can use:

```javascript
router.post("/", (req, res) => {
    const product = req.body;

    res.status(201).json({
        message: "Product Created",
        product: product
    });
});
```

A client can send:

```json
{
    "name": "Laptop",
    "price": 1200
}
```

The router receives the data through:

```javascript
req.body
```

---

# 12. Product Router with Request Body

Example:

```javascript
const express = require("express");

const router = express.Router();

router.get("/", (req, res) => {
    res.json({
        message: "Product List"
    });
});

router.get("/:id", (req, res) => {
    res.json({
        message: "Product Detail",
        id: req.params.id
    });
});

router.post("/", (req, res) => {
    const product = req.body;

    res.status(201).json({
        message: "Product Created",
        product: product
    });
});

router.put("/:id", (req, res) => {
    const id = req.params.id;
    const product = req.body;

    res.json({
        message: "Product Updated",
        id: id,
        product: product
    });
});

router.delete("/:id", (req, res) => {
    res.json({
        message: "Product Deleted",
        id: req.params.id
    });
});

module.exports = router;
```

---

# 13. Creating a User Router

We can create another route module.

Project:

```text
routes/
├── product.routes.js
└── user.routes.js
```

Create:

```text
routes/user.routes.js
```

Example:

```javascript
const express = require("express");

const router = express.Router();

router.get("/", (req, res) => {
    res.json({
        message: "User List"
    });
});

router.get("/:id", (req, res) => {
    res.json({
        message: "User Detail",
        id: req.params.id
    });
});

router.post("/", (req, res) => {
    res.status(201).json({
        message: "User Created"
    });
});

module.exports = router;
```

---

# 14. Connect Multiple Routers

In `server.js`:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

const productRoutes = require("./routes/product.routes");
const userRoutes = require("./routes/user.routes");

app.use("/api/products", productRoutes);
app.use("/api/users", userRoutes);

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

Now the application has:

```text
Product API
/api/products

User API
/api/users
```

---

# 15. API Module Organization

As the application grows, we can organize routes by resource:

```text
routes/
├── product.routes.js
├── user.routes.js
├── category.routes.js
├── order.routes.js
└── auth.routes.js
```

Each file handles a specific group of routes.

Example:

```text
product.routes.js
        ↓
Product API

user.routes.js
        ↓
User API

order.routes.js
        ↓
Order API
```

---

# 16. Router-Level Middleware

A router can have its own middleware.

Example:

```javascript
const logger = (req, res, next) => {
    console.log("Product API request");

    next();
};

router.use(logger);
```

Complete example:

```javascript
const express = require("express");

const router = express.Router();

const logger = (req, res, next) => {
    console.log("Product API request");

    next();
};

router.use(logger);

router.get("/", (req, res) => {
    res.json({
        message: "Product List"
    });
});

module.exports = router;
```

This middleware applies to routes handled by this router.

---

# 17. Router-Level Authentication

Router-level middleware can also be used for authentication.

Example:

```javascript
const authenticate = (req, res, next) => {
    const token = req.headers.authorization;

    if (!token) {
        return res.status(401).json({
            message: "Unauthorized"
        });
    }

    next();
};

router.use(authenticate);
```

This means routes attached to this router can require authentication.

A complete JWT authentication system will be covered in Lesson 10.

---

# 18. Middleware for Specific Router Routes

Middleware can also be applied to a single route.

Example:

```javascript
router.get(
    "/profile",
    authenticate,
    (req, res) => {
        res.json({
            message: "Profile"
        });
    }
);
```

Only:

```text
GET /profile
```

uses this middleware.

---

# 19. Router with Query Parameters

Routers can access query parameters normally.

Example:

```javascript
router.get("/", (req, res) => {
    const category = req.query.category;

    res.json({
        category: category
    });
});
```

Request:

```text
GET /api/products?category=phone
```

Response:

```json
{
    "category": "phone"
}
```

---

# 20. Router with Route Parameters

Routers can also access route parameters.

Example:

```javascript
router.get("/:id", (req, res) => {
    const id = req.params.id;

    res.json({
        productId: id
    });
});
```

Request:

```text
GET /api/products/10
```

Response:

```json
{
    "productId": "10"
}
```

---

# 21. Route File Naming

A common naming style is:

```text
product.routes.js
user.routes.js
order.routes.js
category.routes.js
```

The `.routes.js` suffix makes it clear that the file contains routes.

The exact naming convention can vary by project, but consistency is important.

---

# 22. Recommended Project Structure for This Lesson

A simple application can use:

```text
express-router/
│
├── node_modules/
├── package.json
├── package-lock.json
├── server.js
│
└── routes/
    ├── product.routes.js
    └── user.routes.js
```

Later, the project will become larger:

```text
express-app/
│
├── server.js
│
├── routes/
│   ├── product.routes.js
│   ├── user.routes.js
│   ├── category.routes.js
│   └── order.routes.js
│
├── controllers/
├── services/
├── models/
├── middleware/
└── config/
```

The larger structure will be introduced as the course progresses toward MVC architecture.

---

# 23. `server.js` Responsibility

With routers, `server.js` can focus on application setup.

Example:

```javascript
const express = require("express");

const app = express();

const productRoutes = require("./routes/product.routes");
const userRoutes = require("./routes/user.routes");

app.use(express.json());

app.use("/api/products", productRoutes);
app.use("/api/users", userRoutes);

app.listen(3000, () => {
    console.log("Server running on port 3000");
});
```

Instead of containing every Product and User route, the main application connects route modules.

---

# 24. Request Flow with Router

Consider:

```text
GET /api/products/10
```

The flow is:

```text
Client
   ↓
server.js
   ↓
/api/products
   ↓
productRoutes
   ↓
router.get("/:id")
   ↓
Route Handler
   ↓
Response
```

This makes the application's routing structure easier to understand.

---

# 25. Complete Mini Project

## `server.js`

```javascript
const express = require("express");

const app = express();

app.use(express.json());

const productRoutes = require("./routes/product.routes");

app.use("/api/products", productRoutes);

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

## `routes/product.routes.js`

```javascript
const express = require("express");

const router = express.Router();

router.get("/", (req, res) => {
    res.json({
        message: "Product List"
    });
});

router.get("/:id", (req, res) => {
    res.json({
        message: "Product Detail",
        id: req.params.id
    });
});

router.post("/", (req, res) => {
    res.status(201).json({
        message: "Product Created",
        product: req.body
    });
});

router.put("/:id", (req, res) => {
    res.json({
        message: "Product Updated",
        id: req.params.id,
        product: req.body
    });
});

router.delete("/:id", (req, res) => {
    res.json({
        message: "Product Deleted",
        id: req.params.id
    });
});

module.exports = router;
```

---

# 26. Test the API

Start the server:

```bash
node server.js
```

Test:

```text
GET http://localhost:3000/api/products
```

Test one product:

```text
GET http://localhost:3000/api/products/10
```

Create a product:

```text
POST http://localhost:3000/api/products
```

JSON body:

```json
{
    "name": "Laptop",
    "price": 1200
}
```

Update:

```text
PUT http://localhost:3000/api/products/10
```

Delete:

```text
DELETE http://localhost:3000/api/products/10
```

---

# 27. Practice Exercise

## Exercise 1 — Student Router

Create:

```text
routes/student.routes.js
```

Add:

```text
GET    /api/students
GET    /api/students/:id
POST   /api/students
PUT    /api/students/:id
DELETE /api/students/:id
```

Connect the router in `server.js`.

---

## Exercise 2 — Category Router

Create:

```text
routes/category.routes.js
```

Add:

```text
GET /api/categories
GET /api/categories/:id
POST /api/categories
```

---

## Exercise 3 — Router Middleware

Create a middleware that logs:

```text
Product Router Request
```

Use it only inside:

```text
product.routes.js
```

---

# 28. Lab Practice — E-Commerce API Routers

Create the following structure:

```text
routes/
├── product.routes.js
├── category.routes.js
├── user.routes.js
└── order.routes.js
```

Connect them in `server.js`:

```text
/api/products
/api/categories
/api/users
/api/orders
```

Each router should contain at least:

```text
GET /
GET /:id
POST /
```

For now, use JSON responses instead of a database.

Example:

```javascript
router.get("/", (req, res) => {
    res.json({
        message: "Product List"
    });
});
```

---

# 29. Review Questions

1. What is `express.Router()`?
2. Why should we separate routes into different files?
3. How do you create a router?
4. How do you export a router?
5. How do you import a router into `server.js`?
6. What does this code do?

```javascript
app.use("/api/products", productRoutes);
```

7. Why does the router use `/` instead of `/api/products`?
8. What is router-level middleware?
9. How can middleware be applied to an entire router?
10. How can middleware be applied to only one router route?
11. How do route parameters work inside a router?
12. How do query parameters work inside a router?
13. What is the advantage of organizing routes by resource?
14. What should `server.js` mainly be responsible for after routes are separated?
15. Create a Product Router with CRUD routes.

---

# 30. Key Points

Remember:

```text
express.Router()
       ↓
Create a route module
```

Example:

```javascript
const router = express.Router();
```

Define routes:

```javascript
router.get("/", ...);
router.post("/", ...);
router.get("/:id", ...);
router.put("/:id", ...);
router.delete("/:id", ...);
```

Export:

```javascript
module.exports = router;
```

Connect:

```javascript
app.use("/api/products", productRoutes);
```

Result:

```text
GET    /api/products
GET    /api/products/:id
POST   /api/products
PUT    /api/products/:id
DELETE /api/products/:id
```

---

# Next Lesson

## Lesson 06 — Building REST APIs

Topics:

- REST API concepts
- HTTP methods
- HTTP status codes
- JSON responses
- CRUD operations
- Building a complete Product REST API

API:

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
DELETE  /api/products/:id
```
