---
title: Setup
---

# How to build a login system

We want clients to be able to 'login'. And if they do any API requests after their login, the system should know who they are. To do this, we use a JSON Web Token (JWT).

We don't want the client to send a username/password with every request. That would mean the password has to be remembered on the client's side across multiple requests. Instead, we make the client log in once, and give them back some special _proof_. Something to prove that they are who they say they are. Their claim has to be real, genuine, a.k.a. _authentic_! That proof is the JWT.

In high-school, you have to hand in sick-notes from your parents. Maybe even from a doctor. That piece of paper is a proof that an authority (your parents or your doctor) said something about you. To prevent you from being clever and writing notes yourself, a signature was mandatory. Same with JWTs.

A JWT contains some data (in our case your user ID) and proof that an authority (your server) has written this message (a signature). The signature is set up in such a way that if you change the data, you invalidate the signature. That makes the token tamper-proof. Unlike to a sick-note.

For a very short explanation of how JWTs work, check [this video](https://www.youtube.com/watch?v=K6pwjJ5h0Gg). If you want to go a bit deeper, watch this [hour long presentation](https://www.youtube.com/watch?v=67mezK3NzpU).

# Using JWTs

To sign and verify JWTs, we use a library called `jsonwebtoken`. Add it as a dependency to your project:

```bash
$ npm install jsonwebtoken
```

To make sure someone else cannot forge our signatures, we use a secret key. In our case, we just make up a random string (`const secret` in the code below).

Create a `auth` directory and a `auth/jwt.js`.
In that file, add the following code:

```js
const jwt = require("jsonwebtoken");

const secret = process.env.JWT_SECRET || "e9rp^&^*&@9sejg)DSUA)jpfds8394jdsfn,m";

function toJWT(data) {
  return jwt.sign(data, secret, { expiresIn: "2h" });
}

function toData(token) {
  return jwt.verify(token, secret);
}

module.exports = { toJWT, toData };
```

Let's go through it:

1. We require the module and define a secret key for our JWT. Of course secret keys should not go into Github (it's fine in this case), so that's why we first set it to be equal to an `env` variable we could configure in the hosting of our server, so it's not exposed to the public.
1. The two functions make use of the library's methods. The first one creates a `JWT` token combining the data we hand it (in this case it's going to be our `userId`) and sets it's expiration time. The second one verifys tokens are valid.
1. Finally we export both functions so we can use them in our routes (or middleware!).

## Login route

In the `router` folder, create a file `auth.js` with the following content:

```JavaScript
const { Router } = require('express')
const { toJWT, toData } = require('../auth/jwt')

const router = new Router()

router.post('/login', async (req, res, next) => {
  // Here goes the login logic.
})

module.exports = router
```

Now you will create an `POST /login` endpoint in the authRouter that allows a user to log in.
We want to create a new login resource that contains a JWT.

We should expect an `email` and a `password` in the JSON body.
For now, since we're still testing and learning, we are not going to hook this up to our `user` model, we re simply going allow anybody to login and receive a JWT.

When the email and password are empty, we should return an `HTTP 400 Bad Request` with a descriptive message:

```js
res.status(400).send({
  message: "Please supply a valid email and password",
});
```

If some email and password are given, we should respond with a JWT with a hard-coded `userId` because this is a tutorial:

```js
// normally we would check the password and find the correct user in the database
res.send({
  jwt: toJWT({ userId: 1 }),
});
```

> ✍️ In your `index.js` make sure you require and `.use` the file 'auth/router.js' in the '/' endpoint so that the route is accessible by "/login".

========================

Test your app using HTTPie. The response should look something like this:

```js
http :4000/auth/login email='alice@wonderland.com' password=downtherabbithole
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 153
Content-Type: application/json; charset=utf-8
X-Powered-By: Express

{
    "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEsImlhdCI6MTUzNTM2MjIzMCwiZXhwIjoxNTM1MzY5NDMwfQ.DxFRClbZLP0L-fczkSiNHEiLqYI4HGbC8Ezrh3JhlG8"
}
```

Great! We now have a JWT. In the next chapter we are going to incorporate using this to protect our `/images` endpoint so only authorized people can have access.

At this point we need to add some images to our database! Add three or four using either `seeds` or use your `/images` endpoint with HTTPie POST request. Tip: you can get the `urls` of Google images by right-clicking on them and selecting "Copy Image Address"
