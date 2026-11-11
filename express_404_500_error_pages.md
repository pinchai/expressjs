# Express.js Error Pages: 404 and 500

## 1. 404 Not Found

A **404 Not Found** error occurs when a client requests a URL that does
not match any route in the Express application.

Add this middleware **after all route definitions** in `app.js`:

``` javascript
// 404 - Not Found
app.use((req, res) => {
    res.status(404).json({
        status: "Error",
        statusCode: 404,
        message: `Cannot find ${req.originalUrl} on this server!`
    });
});
```

### How to test 404

1.  Start the Express server:

    ``` bash
    node app.js
    ```

2.  Open a browser or API client such as Bruno or Postman.

3.  Send a request to a route that does not exist:

    ``` http
    GET http://localhost:3000/api/unknown
    ```

4.  Expected response:

    **HTTP status:** `404 Not Found`

    ``` json
    {
        "status": "Error",
        "statusCode": 404,
        "message": "Cannot find /api/unknown on this server!"
    }
    ```

## 2. 500 Internal Server Error

A **500 Internal Server Error** occurs when an unexpected error happens
while the server is processing a request.

Add this error-handling middleware **after the 404 middleware**:

``` javascript
// 500 - Global Error Handler
app.use((err, req, res, next) => {
    console.error(err.stack);

    res.status(500).json({
        status: "Error",
        statusCode: 500,
        message: "Internal Server Error"
    });
});
```

The four parameters `(err, req, res, next)` identify an Express
error-handling middleware.

### How to test 500

Add this temporary test route **before the 404 middleware**:

``` javascript
app.get("/api/error", (req, res, next) => {
    next(new Error("Test internal server error"));
});
```

1.  Restart the Express server.

2.  Send this request using a browser or API client:

    ``` http
    GET http://localhost:3000/api/error
    ```

3.  Expected response:

    **HTTP status:** `500 Internal Server Error`

    ``` json
    {
        "status": "Error",
        "statusCode": 500,
        "message": "Internal Server Error"
    }
    ```

4.  Check the terminal where Express is running. You should see the
    error stack logged there.

## 3. Complete `app.js` example

``` javascript
const express = require("express");
const app = express();

app.use(express.json());

// Application routes
const productRoutes = require("./routes/product.route");
app.use("/api", productRoutes);

// Temporary route for testing 500
app.get("/api/error", (req, res, next) => {
    next(new Error("Test internal server error"));
});

// 404 - Not Found
app.use((req, res) => {
    res.status(404).json({
        status: "Error",
        statusCode: 404,
        message: `Cannot find ${req.originalUrl} on this server!`
    });
});

// 500 - Internal Server Error
app.use((err, req, res, next) => {
    console.error(err.stack);

    res.status(500).json({
        status: "Error",
        statusCode: 500,
        message: "Internal Server Error"
    });
});

app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

> **Important:** Keep the test route before the 404 middleware. Remove
> `/api/error` after testing. The global error handler above
> demonstrates a generic 500 response; production applications may also
> need to handle specific client-error status codes.

## 4. Testing summary

  Test               Request               Expected status
  ------------------ --------------------- ------------------
  Existing route     `GET /api/products`   Depends on route
  Missing route      `GET /api/unknown`    404
  Test error route   `GET /api/error`      500
