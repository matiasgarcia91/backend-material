---
title: Multiple Routers
---

# Multiple Routers

You can use the `Router` class to create different routers for different types of behavior or data.
This is a great way of separating concerns in your server into smaller more manageable files. For example if you are creating a CRUD interface you could use one file per resource and have all routes for that resource live in that file.
You can register multiple routers as middleware of the same app.

> `countriesRouter.js`
>
> ```js
> const { Router } = require("express");
>
> const router = new Router();
>
> router.get("/", (request, response) =>
>   response.send(["The Netherlands", "Belgium", "Luxembourg"])
> );
>
> module.exports = router;
> ```
>
> `languagesRouter.js`
>
> ```js
> const { Router } = require("express");
>
> const router = new Router();
>
> router.get("/", (request, response) => response.send(["Dutch", "French"]));
>
> module.exports = router;
> ```
>
> `index.js`
>
> ```js
> const express = require("express");
> const countriesRouter = require("./countriesRouter");
> const languagesRouter = require("./languagesRouter");
>
> const app = express();
> // You can register your different routers under root URLs.
> // This means that the "/" route you defined in the
> // countriesRouter will be called on a request to "/countries/".
> // This helps keep your index file clean and readable.
> app.use("/countries", countriesRouter);
> app.use("/languages", languagesRouter);
>
> app.listen(5000);
> ```
>
> Result:
>
> ```zsh
> $ http :5000/countries
> HTTP/1.1 200 OK
> Connection: keep-alive
> Content-Length: 2
> Content-Type: application/json; charset=utf-8
> Date: Tue, 25 Jun 2019 20:59:48 GMT
> ETag: W/"2-l9Fw4VUO7kr8CvBlt4zaMCqXZ0w"
> X-Powered-By: Express
>
> ['The Netherlands', 'Belgium', 'Luxembourg']
>
> $ http :5000/langauges
> HTTP/1.1 200 OK
> Connection: keep-alive
> Content-Length: 2
> Content-Type: application/json; charset=utf-8
> Date: Tue, 25 Jun 2019 20:59:48 GMT
> ETag: W/"2-l9Fw4VUO7kr8CvBlt4zaMCqXZ0w"
> X-Powered-By: Express
>
> ['Dutch', 'French']
> ```

Do you understand why the requests return the output shown?
If not, consider asking your classmates or teacher for help.

## Exercise: Class API

1. Inside a file named `studentRouter.js`:
   1. Import `Router` from `express`.
   1. Declare an array named `students` that includes your name as a string.
   1. Instantiate a `new Router` and register a `GET` endpoint on the `/student` route that sends the `students` as the response.
   1. Export the router.
1. Inside a file named `teacherRouter.js`:
   1. Import `Router` from `express`.
   1. Declare an array named `teachers` that includes your teacher's name as a string.
   1. Instantiate a `new Router` and register a `GET` endpoint on the `/teacher` route that sends the `teachers` as the response.
   1. Export the router.
1. Inside a file named `index.js`:
   1. Import `express`, `./studentRouter.js`, and `./teacherRouter.js`.
   1. Create a new express server.
   1. Register both routers with the server's `use` method.
   1. Start the server on port 4000.
1. Start the server with `node index.js`.
1. In another terminal, test both routes with httpie, like:

```zsh
$ http :4000/teacher
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 11
Content-Type: application/json; charset=utf-8
Date: Tue, 30 Jul 2019 16:35:07 GMT
ETag: W/"b-rQ3G8Rzh43vSSYks9WPVGCKAutA"
X-Powered-By: Express

[
    "<teacher>"
]


$ http :4000/student
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 11
Content-Type: application/json; charset=utf-8
Date: Tue, 30 Jul 2019 16:35:31 GMT
ETag: W/"b-vgJtMD+B8Li3qODwR7oW88JHT2s"
X-Powered-By: Express

[
    "<student>"
]
```
