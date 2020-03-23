---
title: The Router Class
tags:
  - Express
sections:
  - 01-separation
  - 02-multiple-routers
---

# The Router Class

Express exports a class called `Router`.

```js
const express = require("express");

const { Router } = express;

const router = new Router();
```

`Router`s have the same route registration methods, like `get` and `post`, that express servers do.
You can register routes on a `Router` like an `app`.

```js
const express = require("express");

const { Router } = express;

const router = new Router();

router.get("/", (request, response) => response.send("Welcome to the homepage!"));
```

To use a `Router`'s routes, you must register it as middleware with an express server.
To register a router, pass it to the server's `use` method.
If you pass a `Router` to `app.use`, its routes will work when you `listen`.

> `index.js`
>
> ```js
> const app = express();
> app.use(router);
>
> const port = 4000;
> app.listen(port);
> ```
>
> ```zsh
> $ node index.js
> ```
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
> Welcome to the homepage!
> ```

## Exercise: Instantiate a Router

1. Create a new project named `router-class`.
1. Install `express`.
1. Create a file named `instantiate.js`.
1. Inside the file:
   1. Import `express`.
   1. Destructure its `Router` class.
   1. Create a `new Router` instance named `router`.
   1. Register an endpoint with `router.get` that listens on the `/hi/:name` route
   1. Add a route handler that sends `'Hi! ${name}'` as the response, you'll have to get the name from the request params.
   1. Create an new express server named `app`.
   1. Register the router as middleware with `app.use`.
   1. Tell the server to `listen` on port 4000.
1. Run `node instantiate.js`.
1. In another terminal, test your server with httpie.

Make sure you see your server saying hi to you!
