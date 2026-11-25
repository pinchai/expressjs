# Student Project Assignment

## Express.js REST API – Mini E-Commerce Management System

**Project Title:** Mini E-Commerce REST API  
**Project Type:** Individual or Group Project  
**Technology Stack:** Node.js, Express.js, MySQL, mysql2, JWT, bcrypt, dotenv, Postman

---

## 1. Project Description

Students are required to develop a RESTful API for a Mini E-Commerce Management System using Express.js and MySQL.

The system must allow users to register and log in, manage product categories, and manage products.

The project must apply the programming concepts learned in Lessons 04–08:

1. Middleware
2. Express Router
3. REST API and CRUD operations
4. MySQL database connection
5. JWT authentication with MySQL

## 2. Required System Modules

The project must contain three main management modules and an authentication module.

### Module 1 – User Management

Students must implement:

- User registration
- User login
- User logout
- Get all users
- Get user details by ID
- Update user information
- Delete user
- Get logged-in user profile
- Password hashing using bcrypt
- JWT token generation and verification
- Protected API endpoints

**User database fields**

| Field | Data Type |
| --- | --- |
| id | INT, PK, AUTO_INCREMENT |
| name | VARCHAR(100) |
| email | VARCHAR(150), UNIQUE |
| password | VARCHAR(255) |
| role | VARCHAR(20) |
| created_at | TIMESTAMP |

**Authentication APIs**

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/api/auth/register` | Register user |
| POST | `/api/auth/login` | Login user |
| POST | `/api/auth/logout` | Logout user |
| GET | `/api/users/profile` | Get current profile |

**User CRUD APIs**

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/api/users` | Get all users |
| GET | `/api/users/:id` | Get user by ID |
| PUT | `/api/users/:id` | Update user |
| DELETE | `/api/users/:id` | Delete user |

### Module 2 – Category Management

Students must implement:

- Create category
- Get all categories
- Get category by ID
- Update category
- Delete category
- Validate category information
- Prevent deletion when a category still has associated products

**Category database fields**

| Field | Data Type |
| --- | --- |
| id | INT, PK, AUTO_INCREMENT |
| name | VARCHAR(100) |
| description | TEXT |
| created_at | TIMESTAMP |

**Category APIs**

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/api/categories` | Get all categories |
| GET | `/api/categories/:id` | Get category by ID |
| POST | `/api/categories` | Create category |
| PUT | `/api/categories/:id` | Update category |
| DELETE | `/api/categories/:id` | Delete category |

### Module 3 – Product Management

Students must implement:

- Create product
- Get all products
- Get product by ID
- Update product
- Delete product
- Associate products with categories
- Display product information with category details
- Validate product information
- Search products by name
- Filter products by category
- Store product price and stock quantity

**Product database fields**

| Field | Data Type |
| --- | --- |
| id | INT, PK, AUTO_INCREMENT |
| name | VARCHAR(150) |
| description | TEXT |
| price | DECIMAL(10,2) |
| stock | INT |
| image | VARCHAR(255), optional |
| category_id | INT, FK |
| created_at | TIMESTAMP |

**Product APIs**

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/api/products` | Get all products |
| GET | `/api/products/:id` | Get product by ID |
| POST | `/api/products` | Create product |
| PUT | `/api/products/:id` | Update product |
| DELETE | `/api/products/:id` | Delete product |

**Additional product endpoints**

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/api/products?search=laptop` | Search products |
| GET | `/api/products?category_id=1` | Filter products by category |

---

## 3. Database Design

Students must create a MySQL database named:

`mini_ecommerce`

The database must contain three tables:

- `users`
- `categories`
- `products`

### Entity Relationship

- One category can contain many products.
- Each product belongs to one category.
- `products.category_id` references `categories.id`.
- Users are independent user records in this project.

Students must implement the relationship using a MySQL foreign key.

### Required SQL Tasks

Students must write SQL statements to:

1. Create the database.
2. Create the three tables.
3. Define primary keys.
4. Define foreign keys.
5. Define unique constraints for user email.
6. Insert sample records for testing.

---

## 4. Project Architecture

Students must organize their project using separate route files and a database connection file.

**Recommended project structure:**

```text
mini-ecommerce-api/
│
├── server.js
├── package.json
├── .env
├── .gitignore
│
├── config/
│   └── database.js
│
├── middleware/
│   └── auth.middleware.js
│
└── routes/
    ├── auth.routes.js
    ├── user.routes.js
    ├── category.routes.js
    └── product.routes.js
```

Students should follow the Express Router approach and use a MySQL connection pool.

---

## 5. Technical Requirements

All students must follow these requirements.

| No. | Requirement | Description |
| ---: | --- | --- |
| 1 | Express.js | Build the backend API |
| 2 | Middleware | Use application-level and custom middleware |
| 3 | Express Router | Separate routes by module |
| 4 | REST API | Use GET, POST, PUT and DELETE |
| 5 | MySQL | Store data permanently |
| 6 | mysql2 | Connect Express.js to MySQL |
| 7 | Connection Pool | Use `mysql.createPool()` |
| 8 | SQL Security | Use parameterized queries |
| 9 | bcrypt | Hash user passwords |
| 10 | JWT | Generate and verify access tokens |
| 11 | dotenv | Store database credentials and JWT secret |
| 12 | Postman | Test all API endpoints |

### Middleware Requirements

Students must create:

1. **Logger middleware** – display HTTP method and URL.
2. **Authentication middleware** – verify JWT from the Authorization header.
3. **Error-handling middleware** – return JSON error responses.

### Authentication and Authorization Rules

- Users must register before logging in.
- Passwords must never be stored as plain text.
- Protected endpoints must require a valid JWT.
- The API must distinguish between `admin` and `user` roles.
- Only administrators should manage categories and products.
- Regular users can view products and categories.

The project should use `Authorization: Bearer TOKEN` for protected requests.

**Logout note:** With stateless JWT authentication, the client should discard its token when logging out. If students want immediate server-side token invalidation, they can implement a token blacklist as an additional feature.

---

## 6. API Testing Requirements

Students must use Postman to test their API.

### Suggested testing sequence

1. **Register a user**  
   `POST /api/auth/register`

2. **Login**  
   `POST /api/auth/login`  
   Obtain the JWT access token.

3. **Test authentication**  
   `GET /api/users/profile`  
   Send the Bearer token.

4. **Create categories**  
   `POST /api/categories`  
   Use an administrator account.

5. **Create products**  
   `POST /api/products`  
   Include a valid `category_id`.

6. **Test CRUD operations**  
   Test GET, POST, PUT and DELETE for users, categories and products.

7. **Test API security**  
   Test missing tokens, invalid tokens and unauthorized user roles.

### Expected HTTP Status Codes

| Status | Meaning | Example |
| --- | --- | --- |
| 200 | OK | Successfully retrieved or updated |
| 201 | Created | Successfully created |
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Missing or invalid token |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Record does not exist |
| 409 | Conflict | Duplicate email |
| 500 | Internal Server Error | Server/database error |

The API should return JSON responses for both successful operations and errors.

---

## 7. Project Deliverables

Each student or group must submit:

1. **Complete Source Code** – All Express.js project files (pushed to GitHub).
2. **Database SQL File** – `database.sql` containing database and table creation statements and sample data.
3. **Postman Collection** – Exported collection containing all API endpoints and test requests.
4. **Project Documentation** – Explain project architecture, modules, database design and API endpoints.
5. **Video Record about Project Demonstration** – Run the project and explain the source code.

**For Group Projects:**

If students work in a group, list each member's responsibilities and provide a demonstration video for each member:

**Members**

1. Heng Dara (work on login module and video for each member)
2. ...
