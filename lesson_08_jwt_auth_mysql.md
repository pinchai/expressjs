# Express.js Course — Lesson 08
## JWT Authentication with MySQL

### Learning Objectives

By the end of this lesson, students should be able to:

- Understand authentication and authorization.
- Understand how JWT authentication works.
- Create a MySQL `users` table.
- Register users with Express.js and MySQL.
- Hash passwords securely with bcrypt.
- Verify passwords during login.
- Generate JWT access tokens.
- Protect Express.js routes with authentication middleware.
- Read JWT tokens from the `Authorization` header.
- Build a basic login and protected API system.

---

# 1. Introduction

Authentication is the process of verifying **who a user is**.

For example:

```text
User
  ↓
Login
  ↓
Email + Password
  ↓
Server
  ↓
Verify User
  ↓
JWT Token
```

After login, the client can send the token when accessing protected API endpoints.

The basic architecture is:

```text
Client
   ↓
Express.js
   ↓
MySQL
   ↓
Users
```

and after successful login:

```text
Client
   ↓
Login
   ↓
Express.js
   ↓
MySQL
   ↓
Verify Password
   ↓
JWT
   ↓
Client
```

---

# 2. Authentication vs Authorization

These concepts are different.

## Authentication

Authentication answers:

```text
Who are you?
```

Example:

```text
Login with email and password
```

## Authorization

Authorization answers:

```text
What are you allowed to access?
```

Example:

```text
Admin → Can manage products
User  → Can view products
```

In this lesson, the main focus is **authentication with JWT**.

Authorization and roles will be expanded later.

---

# 3. What is JWT?

JWT stands for:

```text
JSON Web Token
```

A JWT is a token that can be used to represent authenticated user information.

A common JWT structure is:

```text
Header.Payload.Signature
```

Example:

```text
xxxxx.yyyyy.zzzzz
```

The client receives a token after successful login.

Then the client sends the token with protected requests.

---

# 4. JWT Authentication Flow

The basic flow is:

```text
1. Register
      ↓
2. Store user in MySQL
      ↓
3. Login
      ↓
4. Verify email/password
      ↓
5. Generate JWT
      ↓
6. Client stores token
      ↓
7. Client sends token
      ↓
8. Middleware verifies token
      ↓
9. Protected route
```

---

# 5. Project Structure

For this lesson, use:

```text
express-jwt-mysql/
│
├── node_modules/
├── package.json
├── package-lock.json
├── server.js
│
├── config/
│   └── database.js
│
├── middleware/
│   └── auth.middleware.js
│
└── routes/
    └── auth.routes.js
```

This keeps the database connection, authentication middleware, and routes separate.

---

# 6. Install Required Packages

Create the project:

```bash
mkdir express-jwt-mysql
cd express-jwt-mysql
```

Initialize:

```bash
npm init -y
```

Install Express:

```bash
npm install express
```

Install MySQL:

```bash
npm install mysql2
```

Install bcrypt:

```bash
npm install bcrypt
```

Install JWT:

```bash
npm install jsonwebtoken
```

Install dotenv:

```bash
npm install dotenv
```

You can also install all dependencies together:

```bash
npm install express mysql2 bcrypt jsonwebtoken dotenv
```

---

# 7. Create the MySQL Database

Open MySQL:

```bash
mysql -u root -p
```

Create the database:

```sql
CREATE DATABASE ecommerce;
```

Use it:

```sql
USE ecommerce;
```

---

# 8. Create the Users Table

Create a `users` table:

```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role VARCHAR(20) NOT NULL DEFAULT 'user',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

The table contains:

```text
id
name
email
password
role
created_at
```

---

# 9. Why Store a Password Hash?

Never store a user's plain-text password.

Do not store:

```text
password123
```

Instead, store a password hash such as:

```text
$2b$10$...
```

The application uses bcrypt to hash passwords.

Flow:

```text
User Password
     ↓
bcrypt
     ↓
Password Hash
     ↓
MySQL
```

During login:

```text
Password
   ↓
bcrypt.compare()
   ↓
Stored Hash
   ↓
Match?
```

---

# 10. Database Connection

Create:

```text
config/database.js
```

Add:

```javascript
require("dotenv").config();

const mysql = require("mysql2/promise");

const pool = mysql.createPool({
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
    port: Number(process.env.DB_PORT),
    waitForConnections: true,
    connectionLimit: 10,
    queueLimit: 0
});

module.exports = pool;
```

---

# 11. Environment Variables

Create:

```text
.env
```

Example:

```env
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=ecommerce
DB_PORT=3306

JWT_SECRET=your_long_random_secret
JWT_EXPIRES_IN=1h
```

Do not commit the `.env` file to a public repository.

Add it to:

```text
.gitignore
```

Example:

```text
node_modules/
.env
```

---

# 12. JWT Secret

The application needs a secret key to sign JWT tokens.

Example:

```env
JWT_SECRET=your_long_random_secret
```

The secret should be difficult to guess.

Do not expose it to frontend applications or put it directly into public source code.

---

# 13. Create the Express Server

Create:

```text
server.js
```

Example:

```javascript
require("dotenv").config();

const express = require("express");

const app = express();

app.use(express.json());

const authRoutes = require("./routes/auth.routes");

app.use("/api/auth", authRoutes);

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

---

# 14. Registration API

The registration endpoint will be:

```text
POST /api/auth/register
```

Request body:

```json
{
    "name": "Dara",
    "email": "dara@example.com",
    "password": "123456"
}
```

The process is:

```text
Request
   ↓
Validate input
   ↓
Check email
   ↓
Hash password
   ↓
Insert user into MySQL
   ↓
Return response
```

---

# 15. Check Whether the Email Exists

Before creating the user:

```javascript
const [rows] = await pool.query(
    "SELECT id FROM users WHERE email = ?",
    [email]
);
```

If a user already exists:

```javascript
if (rows.length > 0) {
    return res.status(409).json({
        message: "Email already registered"
    });
}
```

Status:

```text
409 Conflict
```

---

# 16. Hash the Password

Import bcrypt:

```javascript
const bcrypt = require("bcrypt");
```

Hash the password:

```javascript
const hashedPassword = await bcrypt.hash(password, 10);
```

The number:

```text
10
```

is the bcrypt cost factor used in this example.

Never store:

```javascript
password
```

directly in the database.

Store:

```javascript
hashedPassword
```

---

# 17. Insert the User

Example:

```javascript
const [result] = await pool.query(
    `INSERT INTO users
     (name, email, password, role)
     VALUES (?, ?, ?, ?)`,
    [name, email, hashedPassword, "user"]
);
```

The new user's ID is:

```javascript
result.insertId
```

---

# 18. Complete Register Route

Create:

```text
routes/auth.routes.js
```

Example:

```javascript
const express = require("express");
const bcrypt = require("bcrypt");

const pool = require("../config/database");

const router = express.Router();

router.post("/register", async (req, res) => {
    try {
        const { name, email, password } = req.body;

        if (!name || !email || !password) {
            return res.status(400).json({
                message: "Name, email, and password are required"
            });
        }

        const [existingUsers] = await pool.query(
            "SELECT id FROM users WHERE email = ?",
            [email]
        );

        if (existingUsers.length > 0) {
            return res.status(409).json({
                message: "Email already registered"
            });
        }

        const hashedPassword = await bcrypt.hash(
            password,
            10
        );

        const [result] = await pool.query(
            `INSERT INTO users
             (name, email, password, role)
             VALUES (?, ?, ?, ?)`,
            [name, email, hashedPassword, "user"]
        );

        res.status(201).json({
            message: "User registered successfully",
            userId: result.insertId
        });
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});

module.exports = router;
```

---

# 19. Test Registration

Send:

```text
POST http://localhost:3000/api/auth/register
```

JSON:

```json
{
    "name": "Dara",
    "email": "dara@example.com",
    "password": "123456"
}
```

Expected response:

```json
{
    "message": "User registered successfully",
    "userId": 1
}
```

Check MySQL:

```sql
SELECT id, name, email, role, created_at
FROM users;
```

The password column should contain a hash rather than the original password.

---

# 20. Login API

The login endpoint is:

```text
POST /api/auth/login
```

Request:

```json
{
    "email": "dara@example.com",
    "password": "123456"
}
```

The process is:

```text
Login
   ↓
Find user by email
   ↓
Compare password
   ↓
Generate JWT
   ↓
Return token
```

---

# 21. Find User by Email

Use:

```javascript
const [rows] = await pool.query(
    "SELECT * FROM users WHERE email = ?",
    [email]
);
```

If no user exists:

```javascript
if (rows.length === 0) {
    return res.status(401).json({
        message: "Invalid email or password"
    });
}
```

---

# 22. Compare Password

Get the user:

```javascript
const user = rows[0];
```

Compare the submitted password with the stored hash:

```javascript
const isMatch = await bcrypt.compare(
    password,
    user.password
);
```

If the password is incorrect:

```javascript
if (!isMatch) {
    return res.status(401).json({
        message: "Invalid email or password"
    });
}
```

---

# 23. Generate JWT

Import:

```javascript
const jwt = require("jsonwebtoken");
```

Create a token:

```javascript
const token = jwt.sign(
    {
        id: user.id,
        email: user.email,
        role: user.role
    },
    process.env.JWT_SECRET,
    {
        expiresIn: process.env.JWT_EXPIRES_IN || "1h"
    }
);
```

The payload contains information needed by the application.

Do not put sensitive information such as a password into the JWT payload.

---

# 24. Complete Login Route

Add to:

```text
routes/auth.routes.js
```

```javascript
const jwt = require("jsonwebtoken");
```

Then:

```javascript
router.post("/login", async (req, res) => {
    try {
        const { email, password } = req.body;

        if (!email || !password) {
            return res.status(400).json({
                message: "Email and password are required"
            });
        }

        const [rows] = await pool.query(
            "SELECT * FROM users WHERE email = ?",
            [email]
        );

        if (rows.length === 0) {
            return res.status(401).json({
                message: "Invalid email or password"
            });
        }

        const user = rows[0];

        const isMatch = await bcrypt.compare(
            password,
            user.password
        );

        if (!isMatch) {
            return res.status(401).json({
                message: "Invalid email or password"
            });
        }

        const token = jwt.sign(
            {
                id: user.id,
                email: user.email,
                role: user.role
            },
            process.env.JWT_SECRET,
            {
                expiresIn:
                    process.env.JWT_EXPIRES_IN || "1h"
            }
        );

        res.json({
            message: "Login successful",
            token: token
        });
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});
```

---

# 25. Test Login

Request:

```text
POST /api/auth/login
```

Body:

```json
{
    "email": "dara@example.com",
    "password": "123456"
}
```

Response:

```json
{
    "message": "Login successful",
    "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

The client should use this token when accessing protected API endpoints.

---

# 26. Authorization Header

A common way to send the JWT is:

```text
Authorization: Bearer <token>
```

Example:

```text
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

The server can read the header using:

```javascript
req.headers.authorization
```

---

# 27. Authentication Middleware

Create:

```text
middleware/auth.middleware.js
```

Example:

```javascript
const jwt = require("jsonwebtoken");

const authenticate = (req, res, next) => {
    try {
        const authHeader = req.headers.authorization;

        if (!authHeader) {
            return res.status(401).json({
                message: "Authorization header required"
            });
        }

        const [scheme, token] = authHeader.split(" ");

        if (scheme !== "Bearer" || !token) {
            return res.status(401).json({
                message: "Invalid authorization format"
            });
        }

        const decoded = jwt.verify(
            token,
            process.env.JWT_SECRET
        );

        req.user = decoded;

        next();
    } catch (error) {
        return res.status(401).json({
            message: "Invalid or expired token"
        });
    }
};

module.exports = authenticate;
```

---

# 28. How the Middleware Works

Request:

```text
GET /api/profile
Authorization: Bearer TOKEN
```

Flow:

```text
Request
   ↓
auth middleware
   ↓
Read Authorization header
   ↓
Extract Bearer token
   ↓
jwt.verify()
   ↓
Valid?
   ├── No → 401
   │
   └── Yes
         ↓
       req.user
         ↓
       next()
         ↓
     Protected route
```

---

# 29. Protected Route

Create:

```text
routes/user.routes.js
```

Example:

```javascript
const express = require("express");

const authenticate = require("../middleware/auth.middleware");

const router = express.Router();

router.get("/profile", authenticate, (req, res) => {
    res.json({
        message: "Protected profile",
        user: req.user
    });
});

module.exports = router;
```

---

# 30. Connect the User Router

In `server.js`:

```javascript
const userRoutes = require("./routes/user.routes");

app.use("/api/users", userRoutes);
```

Now:

```text
GET /api/users/profile
```

is protected.

---

# 31. Complete Project Structure

The project now looks like:

```text
express-jwt-mysql/
│
├── node_modules/
├── package.json
├── package-lock.json
├── .env
├── .gitignore
├── server.js
│
├── config/
│   └── database.js
│
├── middleware/
│   └── auth.middleware.js
│
└── routes/
    ├── auth.routes.js
    └── user.routes.js
```

Later, Product routes can also be protected.

---

# 32. Complete `server.js`

```javascript
require("dotenv").config();

const express = require("express");

const authRoutes = require("./routes/auth.routes");
const userRoutes = require("./routes/user.routes");

const app = express();

app.use(express.json());

app.use("/api/auth", authRoutes);
app.use("/api/users", userRoutes);

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

---

# 33. Authentication Flow Example

## Step 1 — Register

```text
POST /api/auth/register
```

```json
{
    "name": "Dara",
    "email": "dara@example.com",
    "password": "123456"
}
```

MySQL:

```text
users
------------------------------------------------
id | name  | email | password(hash) | role
------------------------------------------------
1  | Dara  | ...   | $2b$10$...     | user
```

---

## Step 2 — Login

```text
POST /api/auth/login
```

The server:

```text
Find user
   ↓
Compare password
   ↓
Generate JWT
```

---

## Step 3 — Receive Token

```json
{
    "message": "Login successful",
    "token": "JWT_TOKEN"
}
```

---

## Step 4 — Access Protected Route

Request:

```text
GET /api/users/profile
```

Header:

```text
Authorization: Bearer JWT_TOKEN
```

---

## Step 5 — Verify Token

Middleware:

```javascript
jwt.verify(
    token,
    process.env.JWT_SECRET
);
```

If valid:

```javascript
req.user = decoded;
```

Then:

```javascript
next();
```

---

# 34. JWT Payload Example

A token in this lesson can contain:

```json
{
    "id": 1,
    "email": "dara@example.com",
    "role": "user"
}
```

Do not store:

```text
password
password hash
credit card information
other sensitive secrets
```

inside the JWT payload.

---

# 35. Role Information

The MySQL table contains:

```text
role
```

Example:

```text
user
admin
```

The JWT can contain:

```json
{
    "id": 1,
    "email": "admin@example.com",
    "role": "admin"
}
```

This provides information that can later be used for authorization.

For example:

```text
Authentication
       ↓
Is the user logged in?

Authorization
       ↓
Is the user an admin?
```

A complete role-based authorization lesson can be added after the basic JWT system.

---

# 36. Important Security Practices

### 1. Hash passwords

Use:

```javascript
bcrypt.hash()
```

Never store plain-text passwords.

### 2. Use environment variables

Store:

```text
DB_PASSWORD
JWT_SECRET
```

in `.env`.

### 3. Use parameterized SQL

Use:

```javascript
"SELECT * FROM users WHERE email = ?"
```

instead of constructing SQL with user input.

### 4. Do not expose JWT secrets

Keep:

```text
JWT_SECRET
```

server-side.

### 5. Do not put passwords in JWTs

JWT payloads should not contain passwords.

---

# 37. Common HTTP Responses

### 400 — Bad Request

Missing required data:

```json
{
    "message": "Email and password are required"
}
```

### 401 — Unauthorized

Invalid credentials:

```json
{
    "message": "Invalid email or password"
}
```

Invalid token:

```json
{
    "message": "Invalid or expired token"
}
```

### 409 — Conflict

Email already exists:

```json
{
    "message": "Email already registered"
}
```

### 500 — Internal Server Error

Unexpected server/database problem:

```json
{
    "message": "Database error"
}
```

---

# 38. Practice Exercise

## Exercise 1 — Create Users Table

Create:

```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role VARCHAR(20) NOT NULL DEFAULT 'user',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## Exercise 2 — Register

Create:

```text
POST /api/auth/register
```

Requirements:

- Accept name.
- Accept email.
- Accept password.
- Check whether email already exists.
- Hash password with bcrypt.
- Insert user into MySQL.
- Return `201`.

---

## Exercise 3 — Login

Create:

```text
POST /api/auth/login
```

Requirements:

- Find user by email.
- Compare password with bcrypt.
- Generate JWT.
- Return the token.

---

## Exercise 4 — Protected Route

Create:

```text
GET /api/users/profile
```

Requirements:

- Require `Authorization`.
- Extract the Bearer token.
- Verify JWT.
- Return `req.user`.

---

# 39. Lab Practice — JWT Authentication API

Build a complete authentication system.

## Database

Database:

```text
ecommerce
```

Table:

```text
users
```

Fields:

```text
id
name
email
password
role
created_at
```

## API

```text
POST /api/auth/register
POST /api/auth/login
GET  /api/users/profile
```

## Requirements

1. Use Express.js.
2. Use MySQL.
3. Use `mysql2`.
4. Use bcrypt.
5. Use JWT.
6. Use `dotenv`.
7. Use `express.json()`.
8. Hash passwords.
9. Never store plain-text passwords.
10. Use parameterized SQL queries.
11. Return JWT after successful login.
12. Protect `/api/users/profile`.
13. Verify the Bearer token.
14. Return `401` for invalid authentication.
15. Store `JWT_SECRET` in `.env`.

---

# 40. Review Questions

1. What is authentication?
2. What is authorization?
3. What does JWT stand for?
4. What is a JWT used for?
5. What are the three conceptual parts of a JWT?
6. Why should passwords be hashed?
7. What package can be used to hash passwords?
8. What does `bcrypt.hash()` do?
9. What does `bcrypt.compare()` do?
10. What package is used to create and verify JWTs?
11. What does `jwt.sign()` do?
12. What does `jwt.verify()` do?
13. Where should the JWT secret be stored?
14. What is the purpose of the `Authorization` header?
15. What does `Bearer` mean in:

```text
Authorization: Bearer TOKEN
```

16. What is authentication middleware?
17. Why do we use `req.user`?
18. Why should passwords not be included in JWT payloads?
19. Why should SQL queries use parameters?
20. What status code should be returned for invalid authentication?
21. What status code can be returned when an email already exists?
22. Explain the JWT login flow.
23. Build a JWT authentication system using Express.js and MySQL.

---

# 41. Key Points

Remember:

```text
REGISTER
   ↓
Validate User
   ↓
Hash Password
   ↓
MySQL
```

Login:

```text
LOGIN
   ↓
Find User
   ↓
bcrypt.compare()
   ↓
Generate JWT
   ↓
Return Token
```

Protected request:

```text
Client
   ↓
Authorization: Bearer TOKEN
   ↓
Auth Middleware
   ↓
jwt.verify()
   ↓
req.user
   ↓
Protected Route
   ↓
Response
```

Main technologies:

```text
Express.js
    +
MySQL
    +
bcrypt
    +
JWT
    +
dotenv
```

---

# Next Lesson

## Lesson 09 — Request Validation

Topics:

- Validating request bodies
- Validating route parameters
- Validating query parameters
- Returning validation errors
- Using `express-validator`
- Introduction to Zod
