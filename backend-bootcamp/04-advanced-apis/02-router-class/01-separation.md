---
title: Separation
---

# Separation

The `Router` class makes it easy to divide express applications into multiple files.
You can create and export a `Router` instance in one file.
Then, in another file, import the router, create an express app, and register the router as middleware, like:

> `router.js`
>
> ```js
> const { Router } = require("express");
>
> const router = new Router();
> router.get("/", (request, response) => response.send("hello world"));
>
> module.exports = router;
> ```
>
> `index.js`
>
> ```js
> const express = require("express");
> const router = require("./router");
>
> const app = express();
> app.use(router);
>
> app.listen(4000);
> ```
>
> Result:
>
> ```zsh
> $ http :4000
> HTTP/1.1 200 OK
> Connection: keep-alive
> Content-Length: 2
> Content-Type: application/json; charset=utf-8
> Date: Tue, 25 Jun 2019 20:59:48 GMT
> ETag: W/"2-l9Fw4VUO7kr8CvBlt4zaMCqXZ0w"
> X-Powered-By: Express
>
> hello world
> ```

## Exercise: Separate a Router

1. Inside a file named `router.js`:
   1. Import the `Router` class from `express`.
   1. Create a `new Router` instance.
   1. Register a `GET` endpoint on the `/` route that sends `'separated'` as the response.
   1. Export the router.
1. Inside a file named `server.js`:
   1. Import `express` and the router from `./router.js`.
   1. Create a new express server named `app`.
   1. Register the router with `app.use`.
   1. Start the server on port 4000.
1. Start the server with `node server.js`.
1. In another terminal, test your server with httpie, like:

```
$ http :4000
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 9
Content-Type: text/html; charset=utf-8
Date: Tue, 30 Jul 2019 16:03:05 GMT
ETag: W/"9-UT/TEIlAjmi9EyvlzVkB/f+7BkI"
X-Powered-By: Express

separated
```

Make sure you see the `separated` response in your terminal.
