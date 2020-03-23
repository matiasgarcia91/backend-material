---
title: Middlewares
tags:
  - Express
  - Middlewares
---

# What is middleware?

<TeacherContent>
Quick ideas for MC-exercises:

"What kind of use would best fit a route-specific middleware, like in here: `app.post("/posts", someMiddleware, someOtherMiddleware, handler)`?"

- Authentication and validation middleware
- Logging and validation middleware
- Logging and authentication middleware
</TeacherContent>

A web server, conceptually, can be seen as a program where requests come in, and responses come out. In Express, handler functions determine the response by calling the methods on the response object. Middleware are functions that are called before the handler functions. They sit in the "middle". They may handle/complete the request by sending a response, at which point the request-response cycle will be finished. Or, they hand over control to the _next function in line_. The purpose of middleware is to contribute, in some way, to the handling of the request. How? Here are some examples:

1. Validating input
1. Checking whether the user is logged in (authentication)
1. Performing logging for each request
1. Adding some extra meta-data to the response to measure the server's performance

Yes, you could put all of these features in the handler functions themselves, but putting it in middleware allows for reusability and decoupling these features from your handler functions.

Middleware can be added at the application level, and at the route level. At the application level, middleware functions will be called for _every_ request, no matter which handler function is being targetted. At the route level, middleware will only be called if that route matches the requested URI. This example shows you how to do both:

```javascript
// Middleware at the application level will be called for each request
app.use(loggingMiddleware);

// Middleware at the route level will only be called for requests that match this route
app.get("/books/:bookId", bookIdValidatorMiddleware, (req, res) => {
  // ...
});
```

We add middleware functions (see: the two variable names ending in `Middleware`) that have been declared elsewhere. Now we'll look at how to define/implement them.

Middleware functions are the same as handler functions. They take the same arguments and can do the same things. The different names are used to indicate how they are _meant_ to be used. Let's look at an example of the application-level `loggingMiddleware` from the previous snippet:

```javascript
const loggingMiddleware = (req, res, next) => {
  console.log("request received at " + new Date());
  next();
};
```

This middleware will log the start time of each request to the console. The call to `next()` makes sure that the request processing continues to the next middleware (or handler function). Forgetting to call `next()` will result in the request hanging and eventually timing out. What about the `bookIdValidatorMiddleware`?

```javascript
const bookIdValidatorMiddleware = (req, res, next) => {
  if (!Number.isInteger(parseInt(req.params.bookId))) {
    res
      .status(400)
      .json({ message: "Book ID should be an integer, but was: " + req.params.bookId });
  } else {
    next();
  }
};
```

This route-level middleware validates the path parameter, which must be an integer. If the validation fails, the request is immediately handled and the handler function is not called. If the check passes, then `next()` is called and the handler function works as normal. Is this the best middleware? Probably not. This validation logic could be made part of the handler function itself, and the application as a whole would arguably be simpler. However, it illustrates how middleware can be useful at the route level.

## Exercise: Create and use middleware

<small style="font-size: smaller">
Note: in this and following exercises, to keep things short, some headers have been removed from the expected HTTP responses.
</small>

1. Create a new node project using `npm init`, add `express` as a dependency.
1. Copy the following code into a new JavaScript file:

   ```javascript
   const express = require("express");
   const app = express();
   const port = 3000;

   app.get("/", (req, res) => res.send("Hello"));

   app.listen(port, () => console.log(`Example app listening on port ${port}!`));
   ```

1. Create a middleware function with the name `loggingMiddleware`. In addition to logging the current time to the console, this middleware should also send a custom header (using `res.setHeader(name, value)`) to the client. The header name should be `X-Codaisseur-Time`, and its value should be the same timestamp that was logged to the console. Add this middleware at the application level.
1. Test the middleware by making a request to the default route with HTTPie (`http -v :3000`). The middleware should log to the console, and the response should look something like this:
   ```shell
   # HTTP/1.1 200 OK
   # Content-Type: text/html; charset=utf-8
   # X-Codaisseur-Time: Mon Apr 08 2019 17:13:37 GMT+0200 (W. Europe Daylight Time)
   # X-Powered-By: Express
   #
   # Hello
   ```
1. Next, make a middleware function called `failRandomlyMiddleware`. Make it so that 50% of the time (using `Math.random`) the middleware simply calls `next()` and the other 50% of the time it will **end** the request with a `500` status code (and no content). Add this middleware at the route level.
1. Test your middleware by making several requests. Half the time you should get a `200` response with the `"Hello"` message.
1. Add a second route to respond to `/foo`. Do not add the `failRandomlyMiddleware`.
1. Confirm that the `/foo` _does_ include the custom `X-Codaisseur-Time`, but does _not_ fail randomly. Neat thing about true randomness is that you can never be sure... Try several times until you're "confident".

If you are stuck, or want to see a completed solution, look at [this file](https://github.com/Codaisseur/course-content-exercises/blob/master/week-5/advanced-express-apis/middlewares.js).

# The body-parser middleware

HTTP requests such as `POST` and `PUT` requests often contain a body in addition to the headers and route or query parameters. The body of an HTTP request can be the contents of an HTML form, JSON data, or anything else. The content type of the body should normally be specified by the `Content-Type` header of the request. The same way an HTTP response might have a body and `Content-Type`. Parsing the body of a request is a surprisingly complicated process, but luckily `express` comes with it's own set of parsers based of a very popular library called `body-parser`. We can call this parsers and insert them as middlewares into our express app in order to parse different types of payloads. Here is an example:

```javascript
const express = require("express");
const app = express();
const port = 4000;

// parse application/x-www-form-urlencoded
app.use(express.urlencoded({ extended: false }));
// parse application/json
app.use(express.json());

app.post("/", (req, res) => {
  console.log(req.body);
  res.json({
    message: "We received your request body!",
  });
});
app.listen(port, () => console.log("listening on port " + port));
```

Two middleware functions are being created. Calling `express.json` returns a middleware function. You can optionally pass it a configuration object, as we do for `express.urlencoded`. The meaning of the configuration properties are out of scope for this introduction. The middlewares are registered at the application level. Meaning, they will be called for _all_ requests. Each middleware will parse the request's body, only if the content type matches what they expect; they will ignore other requests and pass the processing off to the next middleware. When parsing is successful, all body-parser middlewares put the parsed result in a property of the request object. That way, the subsequent middleware and handler functions have access to the request body by reading it from `req.body`. In our code example, we are logging the request body to the console and responding with a standard message.

## Exercise: Try it out

1. Use the same Node project we set up for the previous example.
1. Copy the above example code into a new JavaScript file called `parsers.js`and run the program.
1. Test the code with this command: `http -v :4000 foo=bar`.
   Thanks to the `-v` flag you should see both the HTTP request and response
1. Confirm that the api logs the request body to the console
1. Now make a similar request but using HTTPie's "form" mode: `http -v --form :4000 foo=bar`
1. Confirm that the HTTP request is of the type `application/x-www-form-urlencoded` and that the express application logs the same information as before.
