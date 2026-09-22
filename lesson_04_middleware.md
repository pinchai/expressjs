# Express.js Course — Lesson 04
## Middleware

### Learning Objectives

By the end of this lesson, students should be able to:

- Explain what middleware is in Express.js.
- Understand how middleware works in the request-response cycle.
- Create application-level middleware.
- Create router-level middleware.
- Use built-in Express middleware.
- Create custom middleware.
- Understand the basic role of error-handling middleware.
- Use `next()` correctly.

---

# 1. What is Middleware?

**Middleware** is a function that runs during the Express.js request-response cycle.

A middleware function can:

- Execute code.
- Read or modify the request.
- Read or modify the response.
- End the request-response cycle.
- Pass control to the next middleware or route handler.

The basic idea is:

```text
Client
   ↓
Request
   ↓
Middleware
   ↓
Route Handler
   ↓
Response
   ↓
Client
```

Middleware is one of the most important concepts in Express.js because it allows us to add reusable behavior to an application.

---

# 2. Basic Middleware Structure

A middleware function commonly has three parameters:

```javascript
(req, res, next)
```

Example:

```javascript
const myMiddleware = (req, res, next) => {
    console.log("Middleware executed");

    next();
};
```

The parameters are:

```text
req
↓
Request object

res
↓
Response object

next
↓
Pass control to the next middleware or handler
```

---

# 3. Understanding `next()`

The `next()` function tells Express.js to continue processing the request.

Example:

```javascript
const logger = (req, res, next) => {
    console.log("Request received");

    next();
};
```

Without `next()` and without sending a response, the request can remain waiting.

Example:

```javascript
const logger = (req, res, next) => {
    console.log("Request received");

    next();
};
```

The flow is:

```text
Request
   ↓
logger
   ↓
next()
   ↓
Next middleware / route
   ↓
Response
```

---

# 4. Creating Your First Middleware

Create a simple Express application:

```javascript
const express = require("express");

const app = express();

const logger = (req, res, next) => {
    console.log("Request received");

    next();
};

app.use(logger);

app.get("/", (req, res) => {
    res.send("Home Page");
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

When the client visits:

```text
GET /
```

the middleware executes before the route handler.

Console:

```text
Request received
```

Browser:

```text
Home Page
```

---

# 5. `app.use()`

`app.use()` is commonly used to register middleware.

Example:

```javascript
app.use(logger);
```

This means the middleware is applied to requests handled by the application from that point onward.

A middleware can also be registered for a specific path:

```javascript
app.use("/api", logger);
```

This middleware will be used for requests beginning with:

```text
/api
```

For example:

```text
/api/products
/api/users
/api/orders
```

---

# 6. Middleware Execution Order

Express.js processes middleware and routes in the order in which they are registered.

Example:

```javascript
app.use((req, res, next) => {
    console.log("Middleware 1");
    next();
});

app.use((req, res, next) => {
    console.log("Middleware 2");
    next();
});

app.get("/", (req, res) => {
    console.log("Route Handler");
    res.send("Hello");
});
```

Request:

```text
GET /
```

Console:

```text
Middleware 1
Middleware 2
Route Handler
```

The flow is:

```text
Request
   ↓
Middleware 1
   ↓
Middleware 2
   ↓
Route Handler
   ↓
Response
```

---

# 7. Middleware Can End the Request

Middleware does not always have to call `next()`.

It can send a response and finish the request.

Example:

```javascript
app.use("/blocked", (req, res) => {
    res.status(403).send("Access Denied");
});
```

Request:

```text
GET /blocked
```

Response:

```text
Access Denied
```

Because the middleware sends the response, it does not need to call:

```javascript
next();
```

---

# 8. Application-Level Middleware

Application-level middleware is registered on the Express application using:

```javascript
app.use()
```

or methods such as:

```javascript
app.get()
app.post()
```

Example:

```javascript
const logger = (req, res, next) => {
    console.log(`${req.method} ${req.url}`);

    next();
};

app.use(logger);
```

This middleware can run for application requests.

---

# 9. Logger Middleware

A logger middleware can display information about incoming requests.

Example:

```javascript
const logger = (req, res, next) => {
    console.log(`${req.method} ${req.url}`);

    next();
};

app.use(logger);
```

If the client requests:

```text
GET /products
```

the console may display:

```text
GET /products
```

For:

```text
POST /products
```

the console may display:

```text
POST /products
```

Logging will become more important when we study application logging and production deployment.

---

# 10. Middleware with Request Information

Middleware can access the request object.

Example:

```javascript
const logger = (req, res, next) => {
    console.log("Method:", req.method);
    console.log("URL:", req.url);

    next();
};
```

Because middleware receives `req`, it can inspect request information before the route handler executes.

---

# 11. Modifying the Request

Middleware can add information to the request object.

Example:

```javascript
const addUser = (req, res, next) => {
    req.user = {
        id: 1,
        name: "Dara"
    };

    next();
};
```

Then a route can access:

```javascript
app.get("/profile", (req, res) => {
    res.json(req.user);
});
```

Complete example:

```javascript
const express = require("express");

const app = express();

const addUser = (req, res, next) => {
    req.user = {
        id: 1,
        name: "Dara"
    };

    next();
};

app.use(addUser);

app.get("/profile", (req, res) => {
    res.json(req.user);
});

app.listen(3000);
```

Response:

```json
{
    "id": 1,
    "name": "Dara"
}
```

This pattern is useful for authentication middleware later in the course.

---

# 12. Built-in Middleware

Express.js provides built-in middleware.

One important example is:

```javascript
express.json()
```

It parses incoming JSON request bodies.

Example:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

app.post("/products", (req, res) => {
    console.log(req.body);

    res.json({
        message: "Product received",
        product: req.body
    });
});

app.listen(3000);
```

A client can send:

```json
{
    "name": "Laptop",
    "price": 1200
}
```

Then:

```javascript
req.body
```

contains the parsed JSON data.

---

# 13. `express.urlencoded()`

Express also provides:

```javascript
express.urlencoded()
```

It parses URL-encoded request bodies.

Example:

```javascript
app.use(express.urlencoded({ extended: true }));
```

This is useful when receiving form data encoded as:

```text
application/x-www-form-urlencoded
```

For example:

```text
name=Dara&age=20
```

---

# 14. Custom Middleware

A **custom middleware** is middleware that we create ourselves.

Example:

```javascript
const checkTime = (req, res, next) => {
    console.log("Checking request time...");

    next();
};

app.use(checkTime);
```

Custom middleware can be used for:

- Logging
- Authentication
- Authorization
- Validation
- Request processing
- Security checks
- Adding request information

These topics will be expanded in later lessons.

---

# 15. Router-Level Middleware

Router-level middleware is middleware associated with an Express Router.

Example:

```javascript
const express = require("express");

const app = express();
const router = express.Router();

const logger = (req, res, next) => {
    console.log("Router middleware");

    next();
};

router.use(logger);

router.get("/products", (req, res) => {
    res.send("Product List");
});

app.use("/api", router);

app.listen(3000);
```

Request:

```text
GET /api/products
```

Flow:

```text
Request
   ↓
/api Router
   ↓
Router Middleware
   ↓
/products Route
   ↓
Response
```

Router-level middleware becomes especially useful when routes are separated into modules.

---

# 16. Application-Level vs Router-Level Middleware

### Application-level

```javascript
app.use(logger);
```

It is attached to the main Express application.

### Router-level

```javascript
router.use(logger);
```

It is attached to a specific router.

Comparison:

| Application-Level | Router-Level |
|---|---|
| Attached to `app` | Attached to `router` |
| Can apply broadly | Can target a group of routes |
| `app.use()` | `router.use()` |
| Useful for global behavior | Useful for route modules |

---

# 17. Middleware for a Specific Route

Middleware can also be used for an individual route.

Example:

```javascript
const checkUser = (req, res, next) => {
    console.log("Checking user");

    next();
};

app.get("/profile", checkUser, (req, res) => {
    res.send("Profile Page");
});
```

The flow is:

```text
GET /profile
      ↓
checkUser
      ↓
Route Handler
      ↓
Response
```

---

# 18. Multiple Middleware Functions

A route can use multiple middleware functions.

Example:

```javascript
const middleware1 = (req, res, next) => {
    console.log("Middleware 1");
    next();
};

const middleware2 = (req, res, next) => {
    console.log("Middleware 2");
    next();
};

app.get(
    "/products",
    middleware1,
    middleware2,
    (req, res) => {
        res.send("Product List");
    }
);
```

Execution:

```text
middleware1
     ↓
middleware2
     ↓
route handler
```

---

# 19. Authentication Middleware Example

A simple authentication-style middleware can check whether a token exists.

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
```

Use it on a protected route:

```javascript
app.get("/profile", authenticate, (req, res) => {
    res.json({
        message: "Profile data"
    });
});
```

This is only a basic example.

A complete JWT authentication system will be covered in Lesson 10.

---

# 20. Error-Handling Middleware

Express.js supports special middleware for handling errors.

Error-handling middleware has **four parameters**:

```javascript
(err, req, res, next)
```

Example:

```javascript
const errorHandler = (err, req, res, next) => {
    console.error(err);

    res.status(500).json({
        message: "Internal Server Error"
    });
};
```

It is registered after the routes:

```javascript
app.use(errorHandler);
```

---

# 21. Passing an Error with `next()`

A middleware can pass an error to the error-handling middleware.

Example:

```javascript
app.get("/error", (req, res, next) => {
    const error = new Error("Something went wrong");

    next(error);
});
```

Then:

```javascript
app.use((err, req, res, next) => {
    res.status(500).json({
        message: err.message
    });
});
```

Response:

```json
{
    "message": "Something went wrong"
}
```

---

# 22. Error Middleware Structure

Normal middleware:

```javascript
(req, res, next)
```

Error-handling middleware:

```javascript
(err, req, res, next)
```

The extra first parameter:

```javascript
err
```

contains the error.

---

# 23. Middleware Flow

A typical Express.js application can contain multiple layers:

```text
Client
   ↓
Application Middleware
   ↓
Router Middleware
   ↓
Authentication Middleware
   ↓
Validation Middleware
   ↓
Controller / Route Handler
   ↓
Error Handler
   ↓
Response
```

Not every request must use every layer, but this structure shows how middleware can organize request processing.

---

# 24. Complete Middleware Example

```javascript
const express = require("express");

const app = express();

app.use(express.json());

const logger = (req, res, next) => {
    console.log(`${req.method} ${req.url}`);

    next();
};

const authenticate = (req, res, next) => {
    const token = req.headers.authorization;

    if (!token) {
        return res.status(401).json({
            message: "Unauthorized"
        });
    }

    next();
};

app.use(logger);

app.get("/", (req, res) => {
    res.send("Home Page");
});

app.get("/profile", authenticate, (req, res) => {
    res.json({
        message: "Profile Page"
    });
});

app.use((err, req, res, next) => {
    console.error(err);

    res.status(500).json({
        message: "Internal Server Error"
    });
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

---

# 25. Important Middleware Rules

### Rule 1 — Call `next()` when continuing

```javascript
const logger = (req, res, next) => {
    console.log("Request");

    next();
};
```

### Rule 2 — Send a response when ending the request

```javascript
const block = (req, res) => {
    res.status(403).send("Forbidden");
};
```

### Rule 3 — Do not normally do both

Avoid:

```javascript
res.send("Hello");
next();
```

A middleware should either continue the processing or end the request appropriately.

---

# 26. Practice Exercise

## Exercise 1 — Logger Middleware

Create middleware that prints:

```text
METHOD: GET
URL: /products
```

for each request.

---

## Exercise 2 — Time Middleware

Create middleware that prints the current date and time:

```text
Request Time: ...
```

Then call:

```javascript
next();
```

---

## Exercise 3 — Custom Request Property

Create middleware that adds:

```javascript
req.student = {
    id: 1,
    name: "Dara"
};
```

Then create:

```text
GET /student
```

Return:

```json
{
    "id": 1,
    "name": "Dara"
}
```

---

## Exercise 4 — Authentication Middleware

Create middleware that checks:

```text
Authorization
```

If it does not exist, return:

```text
401 Unauthorized
```

If it exists, call:

```javascript
next();
```

---

# 27. Lab Practice — API Middleware

Build a small Product API with middleware.

Requirements:

### 1. JSON Middleware

Use:

```javascript
app.use(express.json());
```

### 2. Logger Middleware

Log:

```text
HTTP Method
URL
```

### 3. Authentication Middleware

Protect:

```text
GET /api/profile
```

Require an `Authorization` header.

### 4. Product Routes

Create:

```text
GET /api/products
POST /api/products
GET /api/products/:id
```

### 5. Error Middleware

Create a centralized error-handling middleware.

Basic structure:

```javascript
app.use((err, req, res, next) => {
    res.status(500).json({
        message: err.message
    });
});
```

---

# 28. Review Questions

1. What is middleware in Express.js?
2. What are the three parameters of normal middleware?
3. What is the purpose of `next()`?
4. What happens if middleware neither calls `next()` nor sends a response?
5. What does `app.use()` do?
6. What is application-level middleware?
7. What is router-level middleware?
8. What is built-in middleware?
9. What does `express.json()` do?
10. What does `express.urlencoded()` do?
11. What is custom middleware?
12. How can middleware modify the request object?
13. How can middleware protect a route?
14. What is error-handling middleware?
15. How many parameters does error-handling middleware have?
16. What is the difference between `(req, res, next)` and `(err, req, res, next)`?
17. Why is middleware order important?
18. Can multiple middleware functions be used for one route?
19. Give two real-world uses of middleware.
20. Create a logger middleware and explain how it works.

---

# 29. Key Points

Remember:

```text
Middleware
    ↓
Runs during request/response processing
```

Normal middleware:

```javascript
(req, res, next)
```

Continue:

```javascript
next();
```

Send response:

```javascript
res.send(...);
```

Built-in JSON middleware:

```javascript
app.use(express.json());
```

Router middleware:

```javascript
router.use(middleware);
```

Error middleware:

```javascript
(err, req, res, next)
```

Typical flow:

```text
Client
   ↓
Middleware
   ↓
Middleware
   ↓
Route
   ↓
Response
```

---

# Next Lesson

## Lesson 05 — Express Router

Topics:

- Understanding `express.Router()`
- Creating separate route files
- Organizing routes
- Creating API modules
- Connecting routers to the main application
