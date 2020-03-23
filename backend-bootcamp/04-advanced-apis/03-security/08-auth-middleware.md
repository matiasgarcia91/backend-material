---
title: Auth Middleware
---

# Re-usable authentication

As we extend our app, there will be more endpoints that require the user to be logged in.
If we take a look at the code of that endpoint, we want to propose two improvements:

1. Make the code re-usable
2. Fetch the user that is logged in to make our data useful

## Middleware

As we learned earlier today, middlewares are a way to re-use logic and also to pre-handle requests before they get to the routes (some or all, depending if you place your middlewares at `app` level, `router` level or `route` level). Another useful thing middlewares can do is modify the `request` object to, for example add some more data into it.

As we know we can get the `userId` back when decoding the jwt token, we could also fetch that user and add it to the `request` object. That way when `next` is called and the route handler gets the request it can already know from which user it comes, pretty useful right?

Create a new file `auth/middleware.js`. And try to fill in this middleware skeleton, most of it is already in our `/images/messy` endpoint so you check the logic from there. We need to add the part about fetching the user who is making the request and add this information to the `request` object.

```js
const User = require("../models").user;
const { toData } = require("./jwt");

function auth(req, res, next) {
  // 1. check for authorization header and "split" it.
  // 2. if authorization header is there, auth type is Bearer and we have something at auth[1] we proceed to check the token.
  //    If not, we return a 401 status and the message: 'Please supply some valid credentials
  //    Remember to try/catch the call to "toData()".
  // 3. Use the value returned from "toData()" to look for that user in your database with User.findByPk
  // 4. If not found, call next();
  // 5. If user is found, set it to `req.user = user` and call next();
}

module.exports = auth;
```

Give it a shot, if you need help [here](https://github.com/Codaisseur/course-content-exercises/blob/master/week-5/imageboard-server/auth/middleware.js) is a worked example.

Now import this middleware as `authMiddleware` in `router/auth.js` and register it to a test route like:

```js
router.get("/test-auth", authMiddleware, (req, res) => {
  res.send({
    message: `Thanks for visiting the secret endpoint ${req.user.email}.`,
  });
});
```

Much shorter! And we can use `auth` anywhere in our app if we just include it.

Now test it out by logging in and using your jwt to call `/test-auth` and checking if it responds with the correct user!

If we wanted to apply auth now to the `/images` endpoint it is as simple as importing the middleware in the router file and adding it to the route as in this example. So:

```js
router.get("/", authMiddleware, async (req, res, next) => {
  try {
    const allImages = await Image.findAll();
    res.json(allImages);
  } catch (e) {
    next(e);
  }
});
```

If we want to protect full routers we can also configure the `authMiddleware` when connecting the routers in `index.js`.

```js
app.use("/users", userRouter);
app.use("/images", authMiddleware, imageRouter);
```

Don't set your `authMiddleware` at app level because that way you would be protecting your login route!

With that, we have a fully functioning login/signup app working. Congrats! 🎉
