---
title: REST
---

# REST

Register routes that access your database:

1. Create a new directory called `routers/` and inside create 2 files, `user.js` and `image.js`.
1. Inside those files:
   1. Import the `Router` class from express.
   1. Import the corresponding model.
   1. Instantiate a `router`.
   1. Register a `GET /` route that responds with all the images/users.
   1. Export the router.
1. In the top-level `index.js` file:
   1. Import the `./router/image` as `imageRouter` and `./router/user` as `userRouter`.
   1. Register them both to their corresponing root path (`/images` and `/users`).

Run the server and navigate to `localhost:4000/images` or use httpie to do the request.
You should see an empty array because there are no rows yet in the `images` table.

### Setup parser middleware

Set up your app to handle incoming HTTP requests:

1. Inside `index.js`, register the jsonParser from express, before your routers:

   ```js
   const app = express();
   const jsonParser = express.json();

   app.use(jsonParser);

   app.use("/users", userRouter);
   app.use("/images", imageRouter);
   ```

1. In `router/image.js`, add a `POST /` endpoint that creates a new image in the database.
1. Add an image to your database using the endpoint. Make sure the `url` field matches a real image URL from the internet.
1. Check the table in your database client to make sure the data is correct.
1. Use httpie and your browser to send a `GET` request to the `/image` route.

Make sure you see the image you created in the array.

### Setup up some routes

Setup up routes to:

- Get one image (`GET` in the images router).
- Create a new user (`POST` in the user router).

To give you an example, the user one should en up looking something like this:

```js
router.post("/", async (req, res, next) => {
  try {
    const { email, password, fullName } = req.body;
    if (!email || !password || !fullName) {
      res.status(400).send("missing parameters");
    } else {
      const newUser = await User.create({ email, password, fullName });
      res.json(newUser);
    }
  } catch (e) {
    next(e);
  }
});
```

> Take a moment to look at this route. See how using destructuring allows us to obtain all parameters from `req.body` in one line so we can validate if they are included. Also, see how we pass those parameters to `User.create`. We are creating a new object with the same parameters, right? So we could do directly:
>
> ```js
> const newUser = await User.create(req.body);
> ```
>
> Both alternatives are correct and have their pros and cons. This second one is surely shorter but you may also pass down some unwanted parameter the request included.
>
> The first option, while a bit longer, prevents this from happening and it's also easier to understand when reading. But, if a model contains lots of parameters this might become to big and untidy, so evaluate which one fits best for your specific case.
>
> Remember, writing clean/readable code is one of the best attributes in a coder!
