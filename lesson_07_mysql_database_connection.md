# Express.js Course — Lesson 07
## Database Connection with MySQL

### Learning Objectives

By the end of this lesson, students should be able to:

- Understand how Express.js connects to MySQL.
- Install the MySQL Node.js driver.
- Create a MySQL database and table.
- Configure a MySQL connection.
- Use a connection pool.
- Test the database connection.
- Execute SQL queries from Express.js.
- Perform basic CRUD operations.
- Separate database code from route code.
- Build a simple Product API connected to MySQL.

---

# 1. Introduction

An Express.js application often needs to store data permanently.

In previous lessons, we used an in-memory array:

```javascript
let products = [
    {
        id: 1,
        name: "Laptop",
        price: 1200
    }
];
```

The problem is that data stored in memory disappears when the server stops.

A database provides persistent storage.

In this lesson, we will connect:

```text
Express.js
    ↓
Node.js
    ↓
MySQL
    ↓
Database
```

---

# 2. What is MySQL?

**MySQL** is a relational database management system.

It stores data in tables.

Example:

```text
products
--------------------------------
id | name      | price
--------------------------------
1  | Laptop    | 1200
2  | Phone     | 800
3  | Keyboard  | 80
```

A MySQL database can contain multiple tables:

```text
ecommerce
│
├── users
├── categories
├── products
├── orders
└── order_items
```

These tables will become useful when we build the final E-Commerce REST API.

---

# 3. Why Connect Express.js to MySQL?

Express.js handles HTTP requests.

MySQL stores application data.

Together:

```text
Client
   ↓
Express.js API
   ↓
MySQL
   ↓
Data
```

For example:

```text
GET /api/products
        ↓
Express.js
        ↓
SELECT * FROM products
        ↓
MySQL
        ↓
Product data
        ↓
JSON response
```

---

# 4. Install the MySQL Driver

For Node.js applications, we can use the `mysql2` package.

Install it:

```bash
npm install mysql2
```

Check `package.json`:

```json
{
    "dependencies": {
        "express": "^5.0.0",
        "mysql2": "^3.0.0"
    }
}
```

The exact versions may be different depending on when the packages are installed.

---

# 5. Create the MySQL Database

Open MySQL:

```bash
mysql -u root -p
```

Create a database:

```sql
CREATE DATABASE ecommerce;
```

Select the database:

```sql
USE ecommerce;
```

Verify:

```sql
SHOW DATABASES;
```

---

# 6. Create the Products Table

Create a `products` table:

```sql
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) NOT NULL
);
```

Check the table:

```sql
DESCRIBE products;
```

The structure is:

```text
products
-----------------------------
id      INT
name    VARCHAR(100)
price   DECIMAL(10,2)
```

---

# 7. Insert Sample Data

Insert some products:

```sql
INSERT INTO products (name, price)
VALUES
    ('Laptop', 1200.00),
    ('Phone', 800.00),
    ('Keyboard', 80.00);
```

Check the data:

```sql
SELECT * FROM products;
```

Expected result:

```text
1 | Laptop   | 1200.00
2 | Phone    | 800.00
3 | Keyboard | 80.00
```

---

# 8. Create the Express Project

Create a project:

```bash
mkdir express-mysql-api
cd express-mysql-api
```

Initialize Node.js:

```bash
npm init -y
```

Install dependencies:

```bash
npm install express mysql2
```

Project structure:

```text
express-mysql-api/
│
├── node_modules/
├── package.json
├── package-lock.json
└── server.js
```

---

# 9. Create a MySQL Connection

Create:

```text
server.js
```

Example:

```javascript
const express = require("express");
const mysql = require("mysql2/promise");

const app = express();

app.use(express.json());

const pool = mysql.createPool({
    host: "localhost",
    user: "root",
    password: "your_password",
    database: "ecommerce",
    waitForConnections: true,
    connectionLimit: 10,
    queueLimit: 0
});
```

The pool manages MySQL connections for the application.

---

# 10. Understanding the Connection Configuration

The configuration contains:

```javascript
const pool = mysql.createPool({
    host: "localhost",
    user: "root",
    password: "your_password",
    database: "ecommerce"
});
```

### `host`

```javascript
host: "localhost"
```

Specifies where MySQL is running.

### `user`

```javascript
user: "root"
```

Specifies the MySQL username.

### `password`

```javascript
password: "your_password"
```

Specifies the MySQL password.

### `database`

```javascript
database: "ecommerce"
```

Specifies the database that the application will use.

---

# 11. Why Use a Connection Pool?

Instead of opening a new database connection for every request, a connection pool manages multiple connections.

Example:

```javascript
const pool = mysql.createPool({
    host: "localhost",
    user: "root",
    password: "your_password",
    database: "ecommerce",
    connectionLimit: 10
});
```

Conceptually:

```text
Express.js
    ↓
Connection Pool
    ├── Connection 1
    ├── Connection 2
    ├── Connection 3
    ├── ...
    └── Connection 10
            ↓
          MySQL
```

A pool is useful for applications that handle multiple database requests.

---

# 12. Test the Database Connection

Create a simple connection test:

```javascript
async function testDatabase() {
    try {
        const connection = await pool.getConnection();

        console.log("MySQL connected successfully");

        connection.release();
    } catch (error) {
        console.error("MySQL connection failed:", error.message);
    }
}

testDatabase();
```

Start the server:

```bash
node server.js
```

If successful:

```text
MySQL connected successfully
```

---

# 13. Execute a SELECT Query

We can execute SQL using:

```javascript
pool.query()
```

Example:

```javascript
const [rows] = await pool.query(
    "SELECT * FROM products"
);

console.log(rows);
```

The result contains the rows returned by MySQL.

---

# 14. GET All Products

Create:

```text
GET /api/products
```

Code:

```javascript
app.get("/api/products", async (req, res) => {
    try {
        const [rows] = await pool.query(
            "SELECT * FROM products"
        );

        res.json(rows);
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});
```

Request:

```text
GET /api/products
```

Response:

```json
[
    {
        "id": 1,
        "name": "Laptop",
        "price": "1200.00"
    },
    {
        "id": 2,
        "name": "Phone",
        "price": "800.00"
    }
]
```

The exact JSON representation of decimal values can depend on the MySQL driver configuration.

---

# 15. GET One Product

Create:

```text
GET /api/products/:id
```

Use a parameterized query:

```javascript
app.get("/api/products/:id", async (req, res) => {
    try {
        const id = Number(req.params.id);

        const [rows] = await pool.query(
            "SELECT * FROM products WHERE id = ?",
            [id]
        );

        if (rows.length === 0) {
            return res.status(404).json({
                message: "Product not found"
            });
        }

        res.json(rows[0]);
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});
```

Request:

```text
GET /api/products/1
```

SQL:

```sql
SELECT * FROM products WHERE id = 1;
```

---

# 16. Why Use `?` in SQL Queries?

Use:

```javascript
"SELECT * FROM products WHERE id = ?"
```

and:

```javascript
[id]
```

instead of building SQL with string concatenation.

Avoid:

```javascript
const sql = `SELECT * FROM products WHERE id = ${id}`;
```

Use parameterized queries:

```javascript
const [rows] = await pool.query(
    "SELECT * FROM products WHERE id = ?",
    [id]
);
```

Parameterized queries help separate SQL from user-provided values and reduce SQL injection risk.

---

# 17. POST — Create a Product

Endpoint:

```text
POST /api/products
```

Request body:

```json
{
    "name": "Monitor",
    "price": 300
}
```

Code:

```javascript
app.post("/api/products", async (req, res) => {
    try {
        const { name, price } = req.body;

        const [result] = await pool.query(
            "INSERT INTO products (name, price) VALUES (?, ?)",
            [name, price]
        );

        res.status(201).json({
            id: result.insertId,
            name: name,
            price: price
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

# 18. Understanding `insertId`

When MySQL inserts a row with:

```sql
AUTO_INCREMENT
```

MySQL generates the ID.

`mysql2` provides the generated ID through:

```javascript
result.insertId
```

Example:

```javascript
const [result] = await pool.query(
    "INSERT INTO products (name, price) VALUES (?, ?)",
    [name, price]
);

console.log(result.insertId);
```

---

# 19. PUT — Update a Product

Endpoint:

```text
PUT /api/products/:id
```

Request:

```text
PUT /api/products/1
```

Body:

```json
{
    "name": "Gaming Laptop",
    "price": 1500
}
```

Code:

```javascript
app.put("/api/products/:id", async (req, res) => {
    try {
        const id = Number(req.params.id);
        const { name, price } = req.body;

        const [result] = await pool.query(
            "UPDATE products SET name = ?, price = ? WHERE id = ?",
            [name, price, id]
        );

        if (result.affectedRows === 0) {
            return res.status(404).json({
                message: "Product not found"
            });
        }

        res.json({
            message: "Product updated",
            id: id,
            name: name,
            price: price
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

# 20. DELETE — Delete a Product

Endpoint:

```text
DELETE /api/products/:id
```

Code:

```javascript
app.delete("/api/products/:id", async (req, res) => {
    try {
        const id = Number(req.params.id);

        const [result] = await pool.query(
            "DELETE FROM products WHERE id = ?",
            [id]
        );

        if (result.affectedRows === 0) {
            return res.status(404).json({
                message: "Product not found"
            });
        }

        res.json({
            message: "Product deleted"
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

# 21. Complete Product MySQL API

A complete `server.js` can look like this:

```javascript
const express = require("express");
const mysql = require("mysql2/promise");

const app = express();

app.use(express.json());

const pool = mysql.createPool({
    host: "localhost",
    user: "root",
    password: "your_password",
    database: "ecommerce",
    waitForConnections: true,
    connectionLimit: 10,
    queueLimit: 0
});

// GET all products
app.get("/api/products", async (req, res) => {
    try {
        const [rows] = await pool.query(
            "SELECT * FROM products"
        );

        res.json(rows);
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});

// GET product by ID
app.get("/api/products/:id", async (req, res) => {
    try {
        const id = Number(req.params.id);

        const [rows] = await pool.query(
            "SELECT * FROM products WHERE id = ?",
            [id]
        );

        if (rows.length === 0) {
            return res.status(404).json({
                message: "Product not found"
            });
        }

        res.json(rows[0]);
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});

// CREATE product
app.post("/api/products", async (req, res) => {
    try {
        const { name, price } = req.body;

        const [result] = await pool.query(
            "INSERT INTO products (name, price) VALUES (?, ?)",
            [name, price]
        );

        res.status(201).json({
            id: result.insertId,
            name: name,
            price: price
        });
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});

// UPDATE product
app.put("/api/products/:id", async (req, res) => {
    try {
        const id = Number(req.params.id);
        const { name, price } = req.body;

        const [result] = await pool.query(
            "UPDATE products SET name = ?, price = ? WHERE id = ?",
            [name, price, id]
        );

        if (result.affectedRows === 0) {
            return res.status(404).json({
                message: "Product not found"
            });
        }

        res.json({
            message: "Product updated",
            id: id,
            name: name,
            price: price
        });
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});

// DELETE product
app.delete("/api/products/:id", async (req, res) => {
    try {
        const id = Number(req.params.id);

        const [result] = await pool.query(
            "DELETE FROM products WHERE id = ?",
            [id]
        );

        if (result.affectedRows === 0) {
            return res.status(404).json({
                message: "Product not found"
            });
        }

        res.json({
            message: "Product deleted"
        });
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

---

# 22. Better Project Structure

Putting the MySQL connection directly inside `server.js` is acceptable for a beginner example.

As the project grows, separate the database configuration:

```text
express-mysql-api/
│
├── server.js
│
├── config/
│   └── database.js
│
├── routes/
│   └── product.routes.js
│
├── controllers/
│   └── product.controller.js
│
├── services/
│   └── product.service.js
│
└── models/
    └── product.model.js
```

This prepares the project for the MVC architecture introduced later.

---

# 23. Create `config/database.js`

Move the connection pool into:

```text
config/database.js
```

Example:

```javascript
const mysql = require("mysql2/promise");

const pool = mysql.createPool({
    host: "localhost",
    user: "root",
    password: "your_password",
    database: "ecommerce",
    waitForConnections: true,
    connectionLimit: 10,
    queueLimit: 0
});

module.exports = pool;
```

---

# 24. Use the Database Module

In another file:

```javascript
const pool = require("./config/database");
```

Then:

```javascript
const [rows] = await pool.query(
    "SELECT * FROM products"
);
```

This keeps database configuration in one place.

---

# 25. Environment Variables

Database passwords should not normally be hard-coded in application source code.

Instead of:

```javascript
password: "mypassword"
```

use environment variables.

A common setup is:

```bash
npm install dotenv
```

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
```

---

# 26. Load Environment Variables

At the beginning of the application:

```javascript
require("dotenv").config();
```

Then:

```javascript
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
```

Do not commit sensitive `.env` values to a public repository.

---

# 27. Test Database Connection

A useful database module can expose the pool:

```javascript
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

Then test:

```javascript
const pool = require("./config/database");

async function testDatabase() {
    try {
        const connection = await pool.getConnection();

        console.log("MySQL connected successfully");

        connection.release();
    } catch (error) {
        console.error(
            "MySQL connection failed:",
            error.message
        );
    }
}

testDatabase();
```

---

# 28. Common Database Errors

Students may encounter errors such as:

### Access denied

```text
Access denied for user
```

Possible causes:

- Incorrect username
- Incorrect password
- User does not have permission

### Unknown database

```text
Unknown database 'ecommerce'
```

Make sure the database exists:

```sql
SHOW DATABASES;
```

### Connection refused

Possible causes:

- MySQL server is not running.
- Incorrect host or port.
- Network configuration problem.

Default MySQL port:

```text
3306
```

---

# 29. API Request Flow with MySQL

Example:

```text
GET /api/products
```

Flow:

```text
Client
   ↓
Express Route
   ↓
MySQL Query
   ↓
SELECT * FROM products
   ↓
MySQL Database
   ↓
Rows
   ↓
res.json(rows)
   ↓
Client
```

For POST:

```text
Client
   ↓
POST /api/products
   ↓
req.body
   ↓
INSERT INTO products
   ↓
MySQL
   ↓
insertId
   ↓
JSON Response
```

---

# 30. Practice Exercise

## Exercise 1 — MySQL Database

Create:

```text
ecommerce
```

Create:

```text
products
```

with:

```text
id
name
price
```

Insert at least five products.

---

## Exercise 2 — GET Products

Create:

```text
GET /api/products
```

Return all products from MySQL.

---

## Exercise 3 — GET Product by ID

Create:

```text
GET /api/products/:id
```

Use:

```javascript
req.params.id
```

and a parameterized SQL query.

---

## Exercise 4 — Create Product

Create:

```text
POST /api/products
```

Request body:

```json
{
    "name": "Mouse",
    "price": 25
}
```

Insert the product into MySQL.

---

## Exercise 5 — Update Product

Create:

```text
PUT /api/products/:id
```

Update:

```text
name
price
```

---

## Exercise 6 — Delete Product

Create:

```text
DELETE /api/products/:id
```

Delete the product from MySQL.

---

# 31. Lab Practice — Product REST API with MySQL

Build a complete Product REST API using Express.js and MySQL.

### Database

```text
Database: ecommerce
Table: products
```

Table:

```sql
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) NOT NULL
);
```

### API

```text
GET     /api/products
GET     /api/products/:id
POST    /api/products
PUT     /api/products/:id
DELETE  /api/products/:id
```

### Requirements

1. Use Express.js.
2. Use `mysql2`.
3. Use `express.json()`.
4. Connect to MySQL using a connection pool.
5. Use parameterized SQL queries.
6. Return JSON responses.
7. Return `201` after successful creation.
8. Return `404` when a product does not exist.
9. Handle database errors.
10. Store database configuration separately from route logic.

---

# 32. Review Questions

1. What is MySQL?
2. Why does an Express.js application need a database?
3. What package can Node.js use to connect to MySQL in this lesson?
4. How do you install `mysql2`?
5. What is a MySQL connection pool?
6. What is the default MySQL port?
7. What is the purpose of `mysql.createPool()`?
8. What does `pool.query()` do?
9. How do you select all products?
10. How do you find one product by ID?
11. What is `result.insertId`?
12. What is `result.affectedRows`?
13. Why should parameterized SQL queries be used?
14. Why should database passwords not be hard-coded?
15. What is the purpose of `.env`?
16. What does `dotenv` do?
17. What status code should normally be returned after creating a product?
18. What status code should be returned when a product does not exist?
19. Explain the flow:

```text
Express.js
   ↓
MySQL Query
   ↓
MySQL Database
   ↓
Result
   ↓
JSON Response
```

20. Build a complete Product CRUD API using Express.js and MySQL.

---

# 33. Key Points

Remember:

```text
Express.js
    ↓
mysql2
    ↓
Connection Pool
    ↓
MySQL
    ↓
Tables
    ↓
Rows
```

Install:

```bash
npm install express mysql2
```

Create a pool:

```javascript
const pool = mysql.createPool({
    host: "localhost",
    user: "root",
    password: "your_password",
    database: "ecommerce"
});
```

Query:

```javascript
const [rows] = await pool.query(
    "SELECT * FROM products"
);
```

Parameterized query:

```javascript
const [rows] = await pool.query(
    "SELECT * FROM products WHERE id = ?",
    [id]
);
```

Create:

```sql
INSERT INTO products ...
```

Read:

```sql
SELECT ...
```

Update:

```sql
UPDATE products ...
```

Delete:

```sql
DELETE FROM products ...
```

---

# Next Lesson

## Lesson 08 — Database Integration

The course roadmap originally places database integration at Lesson 08. After learning the MySQL fundamentals in this lesson, the next lesson can expand database integration to:

- MySQL
- PostgreSQL
- MongoDB
- SQLite
- Database connections
- CRUD operations
- Queries
- Models
- Relationships
- Error handling
