# Express.js Course Roadmap

## Lesson 01 — Introduction to Express.js

- What is Express.js?
- Node.js vs. Express.js
- Installing Express.js
- Creating your first Express server
- Understanding `app.listen()`
- Understanding Request and Response

## Lesson 02 — Express.js Routing

- What is routing?
- GET routes
- POST routes
- PUT routes
- PATCH routes
- DELETE routes
- Route parameters
- Query parameters

## Lesson 03 — Request & Response

- `req.params`
- `req.query`
- `req.body`
- `req.headers`
- `res.send()`
- `res.json()`
- `res.status()`
- `res.redirect()`

## Lesson 04 — Middleware

- What is middleware?
- How middleware works
- Application-level middleware
- Router-level middleware
- Built-in middleware
- Custom middleware
- Error-handling middleware

## Lesson 05 — Express Router

- Understanding `express.Router()`
- Creating separate route files
- Organizing routes
- Creating API modules
- Connecting routers to the main application

## Lesson 06 — Building REST APIs

Build a complete Product REST API:

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
DELETE  /api/products/:id
```

Topics:

- REST API concepts
- HTTP methods
- HTTP status codes
- JSON responses
- CRUD operations

## Lesson 07 — MVC Architecture

Learn how to organize an Express application using MVC:

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

Topics:

- Routes
- Controllers
- Services
- Models
- Separation of concerns
- Project structure

## Lesson 08 — Database Integration

Learn how to connect Express.js with different databases:

- MySQL
- PostgreSQL
- MongoDB
- SQLite

Topics:

- Database connection
- CRUD operations
- Queries
- Models
- Relationships
- Error handling

## Lesson 09 — Request Validation

- Validating request bodies
- Validating route parameters
- Validating query parameters
- Returning validation errors
- Using `express-validator`
- Introduction to Zod

## Lesson 10 — Authentication & Authorization

- User registration
- User login
- Password hashing
- JWT authentication
- Access tokens
- Refresh tokens
- Authentication middleware
- Protected routes
- Authorization and roles

## Lesson 11 — Error Handling

Understanding common HTTP errors:

- `400 Bad Request`
- `401 Unauthorized`
- `403 Forbidden`
- `404 Not Found`
- `429 Too Many Requests`
- `500 Internal Server Error`

Topics:

- Custom errors
- Error responses
- Global error-handling middleware
- Centralized error handling

## Lesson 12 — File Upload

- Uploading images
- Uploading documents
- Understanding `multipart/form-data`
- Using Multer
- File validation
- File size limits
- Storing uploaded files

## Lesson 13 — Express.js Security

- CORS
- Helmet
- Rate limiting
- Input validation
- Password security
- Secure HTTP headers
- Protecting API endpoints

## Lesson 14 — Advanced Express.js

- Environment variables
- Configuration management
- Application logging
- API versioning
- Pagination
- Filtering
- Sorting
- Searching

Example:

```text
GET /api/products?page=1&limit=10
GET /api/products?category=phone
GET /api/products?sort=price
GET /api/products?search=iphone
```

## Lesson 15 — Production & Deployment

- Preparing an Express application for production
- Environment configuration
- Process management with PM2
- Nginx reverse proxy
- Docker
- Production logging
- Deployment
- Basic production security

## Final Project

Build a complete **E-Commerce REST API** using Express.js.

Suggested features:

```text
Authentication
    ↓
Users
    ↓
Products
    ↓
Categories
    ↓
Cart
    ↓
Orders
    ↓
Order Items
    ↓
Payment
```

The final project should combine:

- Express.js
- REST API
- MVC architecture
- Database
- Authentication
- JWT
- Validation
- Middleware
- Error handling
- File upload
- Pagination
- Filtering
- Searching
- Security
- Production deployment
