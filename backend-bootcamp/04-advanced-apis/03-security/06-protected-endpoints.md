---
title: Protecting our Images
---

# Protecting our images

Now we want to add a protection to endpoints of our API. Resources you can only access _if you present a valid JWT_. The client should present the JWT in a special header made for this purpose: the **Authorization** header.

In Express, the `req` (request) object not only exposes the `req.body`, but also a `req.headers`. The request headers can be found there, in lowercase. So we should be able to find our header (if it's there) using `req.headers.authorization`. For [reasons explained here](https://security.stackexchange.com/questions/108662/why-is-bearer-required-before-the-token-in-authorization-header-in-a-http-re), we should specify a type of authorization first, then a space and then a JWT.

A request with HTTPie including this header with a token looks something like this:

```bash
$ http :4000/some-endpoint Authorization:"Bearer <token>"
```

So our auth string contains first the word `Bearer`, a space and our JWT token, this is important because we are going to have to do some manipulation on that string to extract the token, something like:

```js
const auth = req.headers.authorization && req.headers.authorization.split(" ");
```

To first check if it exists and then get an array with both components of the string, this way we can check if the header is there and if it contains all we need:

```js
if (auth && auth[0] === "Bearer" && auth[1]) {
  // Here we know we have a header, the correct type of header
  // and that there's something set where the JWT should be, time
  // to check if it's valid!
}
```

For this we use the `toData` function in the `auth/jwt` file. This checks if the token is valid and returns back the decoded info in the token, in our case our `{ userId: 1 }` we used to create it. If not it will throw an exception (error) so it's important to try/catch this.

Take a look at what this would look like all integrated into the GET /images endpoint.

```js
router.get("/", async (req, res, next) => {
  const auth = req.headers.authorization && req.headers.authorization.split(" ");
  if (auth && auth[0] === "Bearer" && auth[1]) {
    try {
      const data = toData(auth[1]);
    } catch (e) {
      res.status(400).send("Invalid JWT token");
    }
    const allImages = await Image.findAll();
    res.json(allImages);
  } else {
    res.status(401).send({
      message: "Please supply some valid credentials",
    });
  }
});
```

It looks a bit complicated doesn't it? And are we going to have to do this for every endpoint we want to protect? Luckily, no, because middlewares are going to come to the rescue and we are going to encapsulate all this logic into one of them. Eitherway it would be good for you to try this endpoint as it is and see how `JWT` works before we dive into the middleware business. So:

1. Copy it into your `image` router but change the router url to something like `/messy` and keep your old `/` endpoint.
1. Do a POST request to `login` to get a jwt token and copy it.
1. Do a GET request to `images/messy` and set the authorization header correctly, remember:

```bash
$ http :4000/images/messy Authorization:"Bearer <token>"
```
