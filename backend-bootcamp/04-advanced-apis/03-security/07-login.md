---
title: Finalize login
---

# Finalize login endpoint

Now that we have signup in place, we can finalize our login endpoint!

Our current login endpoint is using hardcoded userId to generate the `jwt` token and it isn't really looking for users on a table. We are going to have to change it into something like this:

```js
router.post("/login", (req, res) => {
  const { email, password } = req.body;

  if (!email || !password) {
    res.status(400).send({
      message: "Please supply a valid email and password",
    });
  } else {
    // 1. find user based on email address
    // 2. use bcrypt.compareSync to check the recieved password against the stored hash
    // 3. if the password is correct, return a JWT with the userId of the user (user.id)

    res.send({
      jwt: toJWT({ userId: 1 }),
    });
  }
});
```

Try and do this steps on your own, below is an example but before taking a look think it through.

```js
// 1. find user based on email address
const user = await User.findOne({
  where: {
    email: email,
  },
});
if (!user) {
  res.status(400).send({
    message: "User with that email does not exist",
  });
}
// 2. use bcrypt.compareSync to check the password against the stored hash
else if (bcrypt.compareSync(password, user.password)) {
  // 3. if the password is correct, return a JWT with the userId of the user (user.id)
  const jwt = toJWT({ userId: user.id });
  res.send({
    jwt,
  });
} else {
  res.status(400).send({
    message: "Password was incorrect",
  });
}
```

> ✍️ Finalize and test your login endpoint. Does it properly check your password now?
