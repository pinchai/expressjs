# Lesson 09 — EJS Template Engine with Express.js

## Learning Objectives

By the end of this lesson, students will be able to:

- Understand what a template engine is.
- Understand what EJS is and why it is used with Express.js.
- Install and configure EJS.
- Create EJS views.
- Pass data from Express.js to EJS.
- Use EJS output tags.
- Use JavaScript conditionals and loops in EJS.
- Create reusable partial templates.
- Build dynamic HTML pages.
- Handle forms with Express.js and EJS.
- Understand EJS as a Server-Side Rendering (SSR) technology.

---

# 1. What Is a Template Engine?

A template engine allows us to create HTML pages dynamically by combining:

```text
HTML
+
JavaScript / Template Syntax
+
Server Data
        ↓
Dynamic HTML
```

For example, instead of writing:

```html
<h1>Hello Chai</h1>
```

we can create a dynamic page:

```html
<h1>Hello <%= name %></h1>
```

The server provides the value of `name`.

If:

```js
name = "Chai";
```

the generated HTML becomes:

```html
<h1>Hello Chai</h1>
```

---

# 2. What Is EJS?

**EJS** stands for:

> Embedded JavaScript Templates

EJS allows JavaScript code and expressions to be embedded inside HTML templates.

EJS is commonly used with Node.js and Express.js to generate HTML on the server.

Basic example:

```ejs
<h1>Hello <%= name %></h1>
```

Express sends data to the EJS template:

```js
res.render("home", {
    name: "Chai"
});
```

The browser receives normal HTML:

```html
<h1>Hello Chai</h1>
```

The browser does not need to understand EJS.

---

# 3. Is EJS Server-Side Rendering?

Yes.

EJS is commonly used for **Server-Side Rendering (SSR)**.

The basic flow is:

```text
Browser
   |
   | HTTP Request
   ↓
Express.js
   |
   | Get Data
   ↓
Database / Service
   |
   | Data
   ↓
EJS Template
   |
   | Render HTML
   ↓
Express.js
   |
   | HTML Response
   ↓
Browser
```

For example:

```text
GET /products
       ↓
Express Route
       ↓
Product Controller
       ↓
Database
       ↓
products.ejs
       ↓
HTML
       ↓
Browser
```

The server generates the HTML before sending it to the browser.

---

# 4. EJS vs REST API

EJS and REST APIs are different approaches.

## EJS

```text
Browser
   ↓
Express
   ↓
EJS
   ↓
HTML
   ↓
Browser
```

The server returns HTML.

Example:

```js
res.render("products", {
    products
});
```

## REST API

```text
Browser / Mobile App
        ↓
     Express
        ↓
       JSON
        ↓
    Frontend
```

Example:

```js
res.json(products);
```

The server returns JSON.

### Comparison

| EJS | REST API |
|---|---|
| Returns HTML | Returns JSON |
| Server renders page | Client renders UI |
| Useful for traditional web applications | Useful for APIs and SPAs |
| Uses templates | Uses JSON responses |
| SSR | Usually API-based architecture |

EJS can also be used together with APIs in the same Express application.

---

# 5. Installing EJS

Create an Express project:

```bash
mkdir express-ejs-demo
cd express-ejs-demo
npm init -y
```

Install Express:

```bash
npm install express
```

Install EJS:

```bash
npm install ejs
```

Project structure:

```text
express-ejs-demo/
│
├── node_modules/
├── public/
├── views/
│   └── home.ejs
│
├── app.js
└── package.json
```

---

# 6. Configure EJS in Express

Create:

```text
app.js
```

Code:

```js
const express = require("express");

const app = express();

app.set("view engine", "ejs");

app.set("views", "./views");

app.get("/", (req, res) => {
    res.render("home");
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

Important:

```js
app.set("view engine", "ejs");
```

This tells Express to use EJS as the template engine.

---

# 7. Create Your First EJS Page

Create:

```text
views/home.ejs
```

Add:

```ejs
<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
</head>
<body>

    <h1>Hello EJS</h1>

    <p>Welcome to Express.js</p>

</body>
</html>
```

Run:

```bash
node app.js
```

Open:

```text
http://localhost:3000
```

Express renders:

```text
views/home.ejs
```

and sends HTML to the browser.

---

# 8. `res.render()`

With EJS, we normally use:

```js
res.render()
```

Example:

```js
app.get("/", (req, res) => {
    res.render("home");
});
```

The first argument is the template name:

```js
res.render("home");
```

Express looks for:

```text
views/home.ejs
```

---

# 9. Passing Data to EJS

Express can pass data to an EJS template.

Example:

```js
app.get("/", (req, res) => {

    const name = "Chai";

    res.render("home", {
        name: name
    });

});
```

EJS:

```ejs
<h1>Hello <%= name %></h1>
```

Shorter JavaScript syntax:

```js
res.render("home", {
    name
});
```

---

# 10. EJS Output Tag

The most common EJS syntax is:

```ejs
<%= value %>
```

Example:

```ejs
<h1><%= name %></h1>
```

If:

```js
const name = "Dara";
```

The generated HTML is:

```html
<h1>Dara</h1>
```

---

# 11. EJS Escaped Output

Use:

```ejs
<%= value %>
```

This outputs escaped HTML.

Example:

```js
const message = "<strong>Hello</strong>";
```

EJS:

```ejs
<p><%= message %></p>
```

The HTML is escaped rather than interpreted as HTML.

This behavior is useful for reducing HTML injection risks when displaying user-controlled content.

---

# 12. Unescaped HTML

EJS also provides:

```ejs
<%- value %>
```

Example:

```js
const message = "<strong>Hello</strong>";
```

Template:

```ejs
<%- message %>
```

The browser can interpret the result as HTML.

Use unescaped output carefully.

Do not use:

```ejs
<%- userInput %>
```

for untrusted user input unless the content has been safely sanitized.

---

# 13. EJS JavaScript Code Tag

Use:

```ejs
<% %>
```

for JavaScript code that does not directly output a value.

Example:

```ejs
<%
    const title = "Product List";
%>

<h1><%= title %></h1>
```

The JavaScript code executes on the server while rendering the template.

---

# 14. EJS Comments

EJS comments can be written using:

```ejs
<%# This is an EJS comment %>
```

Example:

```ejs
<%# Display page title %>

<h1><%= title %></h1>
```

The comment is not sent to the browser as HTML.

---

# 15. Passing Multiple Values

Express:

```js
app.get("/", (req, res) => {

    const name = "Chai";
    const age = 25;
    const city = "Phnom Penh";

    res.render("home", {
        name,
        age,
        city
    });

});
```

EJS:

```ejs
<h1>Student Information</h1>

<p>Name: <%= name %></p>
<p>Age: <%= age %></p>
<p>City: <%= city %></p>
```

---

# 16. Conditional Rendering

EJS allows normal JavaScript conditions.

Example:

```ejs
<% if (age >= 18) { %>

    <p>Adult</p>

<% } else { %>

    <p>Minor</p>

<% } %>
```

Express:

```js
app.get("/student", (req, res) => {

    const student = {
        name: "Dara",
        age: 20
    };

    res.render("student", {
        student
    });

});
```

EJS:

```ejs
<h1><%= student.name %></h1>

<% if (student.age >= 18) { %>
    <p>Adult</p>
<% } else { %>
    <p>Minor</p>
<% } %>
```

---

# 17. Multiple Conditions

Example:

```ejs
<% if (score >= 90) { %>

    <p>Grade A</p>

<% } else if (score >= 80) { %>

    <p>Grade B</p>

<% } else if (score >= 70) { %>

    <p>Grade C</p>

<% } else { %>

    <p>Grade F</p>

<% } %>
```

This is normal JavaScript syntax inside an EJS template.

---

# 18. Looping Through an Array

Suppose Express sends:

```js
const products = [
    {
        id: 1,
        name: "Laptop",
        price: 800
    },
    {
        id: 2,
        name: "Phone",
        price: 500
    },
    {
        id: 3,
        name: "Keyboard",
        price: 50
    }
];

res.render("products", {
    products
});
```

EJS:

```ejs
<h1>Products</h1>

<ul>

<% products.forEach(product => { %>

    <li>
        <%= product.name %>
        - $<%= product.price %>
    </li>

<% }) %>

</ul>
```

---

# 19. Product Table Example

EJS:

```ejs
<table border="1">

    <thead>
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Price</th>
        </tr>
    </thead>

    <tbody>

        <% products.forEach(product => { %>

            <tr>
                <td><%= product.id %></td>
                <td><%= product.name %></td>
                <td>$<%= product.price %></td>
            </tr>

        <% }) %>

    </tbody>

</table>
```

---

# 20. Using `map()` in EJS

JavaScript array methods can also be used.

Example:

```ejs
<ul>

<% products.map(product => { %>

    <li>
        <%= product.name %>
    </li>

<% }) %>

</ul>
```

For template readability, `forEach()` is generally easier for beginners.

---

# 21. Using `for` Loops

Example:

```ejs
<% for (let i = 0; i < products.length; i++) { %>

    <p>
        <%= products[i].name %>
    </p>

<% } %>
```

---

# 22. Reusable EJS Partials

A large application should not put all HTML into one file.

For example:

```text
views/
│
├── home.ejs
│
└── partials/
    ├── header.ejs
    ├── navbar.ejs
    └── footer.ejs
```

This allows us to reuse common HTML.

---

# 23. Header Partial

Create:

```text
views/partials/header.ejs
```

```ejs
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title><%= title %></title>
</head>
<body>
```

---

# 24. Navbar Partial

Create:

```text
views/partials/navbar.ejs
```

```ejs
<nav>

    <a href="/">Home</a>
    <a href="/products">Products</a>
    <a href="/about">About</a>

</nav>
```

---

# 25. Footer Partial

Create:

```text
views/partials/footer.ejs
```

```ejs
<footer>

    <p>&copy; 2026 Express EJS Application</p>

</footer>

</body>
</html>
```

---

# 26. Include Partials

In `home.ejs`:

```ejs
<%- include("partials/header") %>

<%- include("partials/navbar") %>

<h1>Home Page</h1>

<p>Welcome to our application.</p>

<%- include("partials/footer") %>
```

The structure becomes:

```text
header
   ↓
navbar
   ↓
page content
   ↓
footer
```

---

# 27. Passing Data to Partials

Suppose:

```js
res.render("home", {
    title: "Home Page",
    name: "Chai"
});
```

Header:

```ejs
<title><%= title %></title>
```

Home:

```ejs
<h1>Hello <%= name %></h1>
```

Variables available to the parent template can generally be used by included templates.

You can also explicitly pass values:

```ejs
<%- include("partials/header", {
    title: "Product Page"
}) %>
```

---

# 28. Serving Static Files

EJS generates HTML, but applications also need:

```text
CSS
JavaScript
Images
Fonts
```

Create:

```text
public/
├── css/
│   └── style.css
├── js/
│   └── app.js
└── images/
```

Configure Express:

```js
app.use(express.static("public"));
```

Project:

```text
project/
│
├── public/
│   ├── css/
│   ├── js/
│   └── images/
│
├── views/
│   └── home.ejs
│
└── app.js
```

---

# 29. Linking CSS from EJS

Create:

```text
public/css/style.css
```

Example:

```css
body {
    font-family: Arial, sans-serif;
    margin: 40px;
}

h1 {
    color: #333;
}
```

In EJS:

```html
<link rel="stylesheet" href="/css/style.css">
```

Because:

```js
app.use(express.static("public"));
```

Express serves the `public` directory.

---

# 30. EJS Forms

EJS can create normal HTML forms.

Example:

```ejs
<form action="/students" method="POST">

    <label>Name</label>

    <input
        type="text"
        name="name"
    >

    <label>Email</label>

    <input
        type="email"
        name="email"
    >

    <button type="submit">
        Save
    </button>

</form>
```

---

# 31. Express Form Body

To read form data:

```js
app.use(express.urlencoded({
    extended: true
}));
```

Then:

```js
app.post("/students", (req, res) => {

    console.log(req.body);

    res.send("Student created");

});
```

For example:

```text
req.body

{
    name: "Dara",
    email: "dara@example.com"
}
```

---

# 32. Complete Student Example

Express:

```js
const express = require("express");

const app = express();

app.set("view engine", "ejs");

app.use(express.urlencoded({
    extended: true
}));

app.get("/students/create", (req, res) => {

    res.render("students/create");

});

app.post("/students", (req, res) => {

    const { name, email } = req.body;

    console.log(name);
    console.log(email);

    res.send("Student created");

});

app.listen(3000, () => {
    console.log("Server running on port 3000");
});
```

View:

```text
views/
└── students/
    └── create.ejs
```

`create.ejs`:

```ejs
<!DOCTYPE html>
<html>
<head>
    <title>Create Student</title>
</head>

<body>

    <h1>Create Student</h1>

    <form action="/students" method="POST">

        <div>
            <label>Name</label>

            <input
                type="text"
                name="name"
                required
            >
        </div>

        <div>
            <label>Email</label>

            <input
                type="email"
                name="email"
                required
            >
        </div>

        <button type="submit">
            Save
        </button>

    </form>

</body>
</html>
```

---

# 33. EJS with MVC

EJS works well with the MVC architecture introduced earlier.

```text
Request
   ↓
Routes
   ↓
Controller
   ↓
Service
   ↓
Model
   ↓
Database
   ↓
Controller
   ↓
EJS View
   ↓
HTML
   ↓
Browser
```

Example project:

```text
express-app/
│
├── controllers/
│   └── productController.js
│
├── models/
│   └── productModel.js
│
├── routes/
│   └── productRoutes.js
│
├── services/
│   └── productService.js
│
├── views/
│   ├── partials/
│   │   ├── header.ejs
│   │   ├── navbar.ejs
│   │   └── footer.ejs
│   │
│   └── products/
│       ├── index.ejs
│       ├── create.ejs
│       ├── edit.ejs
│       └── show.ejs
│
├── public/
│
└── app.js
```

---

# 34. Controller Rendering a View

Example:

```js
const productService = require("../services/productService");

exports.index = async (req, res) => {

    const products = await productService.getAllProducts();

    res.render("products/index", {
        title: "Products",
        products
    });

};
```

The controller does not need to manually construct HTML.

It sends data to EJS.

---

# 35. EJS with Database Data

A typical application flow is:

```text
Browser
   ↓
GET /products
   ↓
Express Router
   ↓
Product Controller
   ↓
Product Service
   ↓
MySQL
   ↓
Products
   ↓
EJS
   ↓
HTML
   ↓
Browser
```

Example:

```js
exports.index = async (req, res) => {

    const products = await productService.getAllProducts();

    res.render("products/index", {
        products
    });

};
```

EJS:

```ejs
<h1>Products</h1>

<% products.forEach(product => { %>

    <div>
        <h2><%= product.name %></h2>

        <p>
            Price: $<%= product.price %>
        </p>
    </div>

<% }) %>
```

---

# 36. EJS Page Structure

A typical EJS application can be organized like this:

```text
views/
│
├── partials/
│   ├── header.ejs
│   ├── navbar.ejs
│   └── footer.ejs
│
├── home.ejs
│
├── products/
│   ├── index.ejs
│   ├── create.ejs
│   ├── edit.ejs
│   └── show.ejs
│
└── students/
    ├── index.ejs
    ├── create.ejs
    ├── edit.ejs
    └── show.ejs
```

This structure works well for CRUD applications.

---

# 37. EJS CRUD Page Flow

For a product management system:

```text
/products
     ↓
Product List
     |
     +---- Create
     |
     +---- Show
     |
     +---- Edit
     |
     +---- Delete
```

Typical routes:

```text
GET     /products
GET     /products/create
POST    /products
GET     /products/:id
GET     /products/:id/edit
POST    /products/:id/update
POST    /products/:id/delete
```

EJS provides the HTML interface.

Express handles the requests.

The database stores the data.

---

# 38. Important EJS Syntax

| Syntax | Purpose |
|---|---|
| `<%= value %>` | Output escaped value |
| `<%- value %>` | Output unescaped value |
| `<% code %>` | Execute JavaScript |
| `<%# comment %>` | EJS comment |
| `<%- include(...) %>` | Include another EJS file |

Example:

```ejs
<h1><%= title %></h1>

<% if (products.length > 0) { %>

    <% products.forEach(product => { %>

        <p><%= product.name %></p>

    <% }) %>

<% } else { %>

    <p>No products found.</p>

<% } %>
```

---

# 39. Common Mistakes

## Mistake 1 — Forgetting the view engine

Incorrect:

```js
const app = express();

app.get("/", (req, res) => {
    res.render("home");
});
```

Correct:

```js
app.set("view engine", "ejs");
```

---

## Mistake 2 — Wrong View Location

If:

```js
res.render("home");
```

Express normally expects:

```text
views/home.ejs
```

---

## Mistake 3 — Forgetting `express.urlencoded()`

For HTML forms:

```js
app.use(express.urlencoded({
    extended: true
}));
```

Without it, form data may not be available through:

```js
req.body
```

---

## Mistake 4 — Using the Wrong EJS Tag

Output:

```ejs
<%= name %>
```

JavaScript code:

```ejs
<% if (condition) { %>
```

Include:

```ejs
<%- include("partials/navbar") %>
```

---

# 40. EJS Security Considerations

Be careful when rendering user-controlled data.

Prefer:

```ejs
<%= user.name %>
```

instead of:

```ejs
<%- user.name %>
```

The escaped form helps prevent HTML from being interpreted as markup.

Never blindly render untrusted content as HTML.

For example, avoid:

```ejs
<%- user.comment %>
```

unless the content has been properly sanitized.

---

# 41. EJS and Express Architecture

EJS is the **View** in an MVC application.

```text
              Express.js
                  |
        +---------+---------+
        |                   |
      Router            Middleware
        |
    Controller
        |
     Service
        |
      Model
        |
    Database
        |
        ↓
    Controller
        |
        ↓
       EJS
        |
        ↓
      HTML
        |
        ↓
     Browser
```

This separation makes applications easier to maintain.

---

# 42. Mini Project — Student Management System

Build a simple Student Management System using:

```text
Express.js
EJS
MySQL
MVC
```

Features:

```text
Students
│
├── List Students
├── View Student
├── Create Student
├── Edit Student
└── Delete Student
```

Suggested project structure:

```text
student-management/
│
├── controllers/
│   └── studentController.js
│
├── models/
│   └── studentModel.js
│
├── routes/
│   └── studentRoutes.js
│
├── services/
│   └── studentService.js
│
├── views/
│   ├── partials/
│   │   ├── header.ejs
│   │   ├── navbar.ejs
│   │   └── footer.ejs
│   │
│   └── students/
│       ├── index.ejs
│       ├── create.ejs
│       ├── edit.ejs
│       └── show.ejs
│
├── public/
│   ├── css/
│   ├── js/
│   └── images/
│
├── app.js
└── package.json
```

---

# 43. Practice Exercise

## Exercise 1 — Hello EJS

Create an Express application that displays:

```text
Welcome to EJS
```

Use:

```js
res.render()
```

---

## Exercise 2 — Student Information

Pass this object from Express:

```js
const student = {
    name: "Dara",
    age: 21,
    gender: "Male",
    major: "Computer Science"
};
```

Display all information using EJS.

---

## Exercise 3 — Product List

Create an array:

```js
const products = [
    {
        id: 1,
        name: "Laptop",
        price: 800
    },
    {
        id: 2,
        name: "Phone",
        price: 500
    },
    {
        id: 3,
        name: "Mouse",
        price: 20
    }
];
```

Display the products in an HTML table.

---

## Exercise 4 — Conditional Rendering

Display:

```text
In Stock
```

when:

```js
stock > 0
```

Otherwise display:

```text
Out of Stock
```

---

## Exercise 5 — Partials

Create:

```text
header.ejs
navbar.ejs
footer.ejs
```

Use them in:

```text
home.ejs
products.ejs
about.ejs
```

---

## Exercise 6 — Student Form

Create a student registration form:

```text
Name
Email
Gender
Phone
Address
```

Submit the form to:

```text
POST /students
```

Display the submitted data on the server.

---

# 44. Lab Exercise — Product Management UI

Build an EJS-based Product Management application.

### Requirements

Create:

```text
GET     /products
GET     /products/create
POST    /products
GET     /products/:id
GET     /products/:id/edit
POST    /products/:id/update
POST    /products/:id/delete
```

The UI should contain:

### Product List

```text
Product ID
Product Name
Price
Quantity
Category
Actions
```

Actions:

```text
View
Edit
Delete
```

### Create Product

Form:

```text
Name
Price
Quantity
Category
Description
```

### Product Detail

Display:

```text
Product Name
Price
Quantity
Category
Description
```

---

# 45. Key Takeaways

After completing this lesson, remember:

1. EJS means **Embedded JavaScript Templates**.
2. EJS is a template engine commonly used with Express.js.
3. EJS can be used for **Server-Side Rendering (SSR)**.
4. Express configures EJS using:

```js
app.set("view engine", "ejs");
```

5. Render an EJS page using:

```js
res.render("home");
```

6. Pass data using:

```js
res.render("home", {
    name: "Chai"
});
```

7. Display data using:

```ejs
<%= name %>
```

8. Execute JavaScript using:

```ejs
<% code %>
```

9. Include reusable templates using:

```ejs
<%- include("partials/header") %>
```

10. EJS works naturally with Express MVC applications.

---

# 46. Lesson Summary

The main architecture learned in this lesson is:

```text
                 Browser
                    |
                    | HTTP Request
                    ↓
              Express Router
                    |
                    ↓
               Controller
                    |
                    ↓
                Service
                    |
                    ↓
                Database
                    |
                    ↓
                 Data
                    |
                    ↓
                  EJS
                    |
                    ↓
               HTML Page
                    |
                    ↓
                 Browser
```

EJS provides the **View layer** of the Express application.

It allows the server to combine:

```text
HTML
+
JavaScript
+
Application Data
```

and generate dynamic HTML pages for the browser.

---

# 47. Next Lesson

The next major topic in the course is:

## Lesson 10 — Authentication & Authorization

Topics include:

- User registration
- User login
- Password hashing
- JWT authentication
- Access tokens
- Refresh tokens
- Authentication middleware
- Protected routes
- Authorization and roles
