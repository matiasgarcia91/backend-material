---
title: Hashing passwords
---

# Create a user

Logging in is now possible, we even have a protected endpoint that you have to authenticate for. As you might remember, we're still not checking emails and passwords yet because there are no user accounts.

In a previous exercise we created the POST /users endpoint. If you didn't do it don't worry, that was just as an exercise and we are going to have to modify a bit that simple `create` endpoint to solve one important concern:

**We are storing a user with a plaintext-password!** Anybody with access to the database can read passwords from anybody! Is that a problem? Yes! It's a very big security risk. Never store a password in plain text in your database.

A great solution is [bcrypt](https://github.com/kelektiv/node.bcrypt.js). It's one of the most recommended algorithms by modern security experts. Bcrypt provides two functions: one to convert a password into an unreadable "hash" string, and another to check if a password matches a hash.

Install `bcrypt` and import it in `user/router.js`
In our signup endpoint we need to transform our incoming password (`req.body.password`) using the `hashSync` function:

```js
const { email, password, fullName } = req.body;
if (!email || !password || !fullName) {
  res.status(400).send("missing parameters");
} else {
  const newUser = await User.create({
    email,
    // Here, when handing down the password to the create method we hash it.
    password: bcrypt.hashSync(password, 10),
    fullName,
  });

  res.json(newUser);
}
```

> ✍️ Adjust your endpoint using the above snippet. Test your routes and make sure passwords are **never** present in the JSON response.

When everything is working, create yourself a couple shiny new users, and remember their passwords!
