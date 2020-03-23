---
title: Building a REST API
---

# Building a REST API

To build a RESTful JSON API with Express, we have to do the following:

1. Implement the basic CRUD (create, read, update and delete) actions using the appropriate HTTP methods.
1. Respond with the correct status codes.
1. Parse the HTTP request body, when necessary, and use it accordingly.
1. Define routes that satisfy the REST constraints.
1. Persist the data (in most cases, anyway).

We are going to build a web service on top of our Sequelize TodoListApp. It will expose the `todoItem`, `todoList` and `user` resources. Consider the following Express routes which we will aim to implement.

```javascript
// Create a new user account
app.post("/users", (req, res, next) => {
  /*..*/
});
// Get a user's information
app.get("/users/:userId", (req, res, next) => {
  /*..*/
});
// Update a user's information
app.put("/users/:userId", (req, res, next) => {
  /*..*/
});
// Get all user's tasks
app.get("/users/:userId/lists", (req, res, next) => {
  /*..*/
});
// Get a single user task
app.get("/users/:userId/lists/:listId", (req, res, next) => {
  /*..*/
});
// Create a new task
app.post("/users/:userId/lists", (req, res, next) => {
  /*..*/
});
// Update an existing task
app.put("/users/:userId/lists/:listId", (req, res, next) => {
  /*..*/
});
// Delete a user's task
app.delete("/users/:userId/lists/:listId", (req, res, next) => {
  /*..*/
});
// Delete all user's tasks
app.delete("/users/:userId/lists", (req, res, next) => {
  /*..*/
});

// And so on for todoItems. And this is only for the User model!
// We could do the same for each of our models.
```

Don't worry, we won't aim to implementing all of these today, just some examples of how the REST principles would apply to each case. The rest will be up to you. Also, we don't have to implement every possible operation on our API for it to be RESTful, but we will follow the REST pattern for the resources/operations we need.
To make this data persist, we are going to have to connect these routes with our Sequelize backend, so information can get stored and retrieved from our database.

## Exercise: Setup an Express app

1. Continue with the Node.js and Sequelize project from the previous exercises.
1. Install the following dependencies if you don't have them already:
   - `express`
1. Create or reuse a `index.js` file in the root of the project. Import the `User` model and create an express app. We are going to configure the JSON body-parser middleware at the application level. This helps us parse the body of HTTP requests, we'll see more on this tomorrow. For now, do:

   ```js
   const express = require("express");
   const app = express();
   const PORT = 4000;

   const User = require("./models").user;

   app.use(express.json());
   ```

   Remember to call `app.listen` to start the web server.

1. To test your setup, add the following route:
   ```javascript
   app.post("/echo", (req, res) => {
     res.json(req.body);
   });
   ```
   Start your server and make a `POST` request using HTTPie to `/echo` like:
   ```bash
   $ http POST :4000/echo hello=world
   ```
   Confirm that your JSON body is returned (echo'd) in the response.

# Creating a new user resource

The "create" action should add a row to the `user` table. We are going to expect all the properties our user model has. Because the `user` gets an `id` from the database, we _must_ return that information to the client. Even better would be to respond with the whole user record. Consider the following route definition:

```javascript
// Create a new user account
app.post("/users", async (req, res, next) => {
  try {
    const user = await User.create(req.body);
    res.json(user);
  } catch (e) {
    next(e);
  }
});
```

Here we pass the body of the request directly to our model. That means we are expecting the body of the request to be a JSON object with all the properties we expect, like:

```j
{
    "name": "Tim",
    "email" "timmy@gmail.com",
    ...
}
```

We can do some check on the request body before attempting to create the `user`. For example, check if an `email` was provided in the request:

```js
app.post("/users", async (req, res, next) => {
  try {
    const email = req.body.email;
    if (!email || email === " ") {
      res.status(400).send("Must provide an email address");
    } else {
      const user = await User.create(req.body);
      res.json(user);
    }
  } catch (e) {
    next(e);
  }
});
```

## Exercise: Create and read users

1. Continue with the Node.js project from the previous exercise.
1. Add the `app.post('/users'` route (provided above) to your Express app.
1. Make a `POST` request to `/users` with an `email` and confirm that the data (including `id`) is returned in the response.
1. Try creating a second user with the same `email`. Because the `email` has to be unique in the database, you should see an error in both your application logs as well as the HTTP response.
1. Add a route definition that will respond to `GET` requests to `/users/:userId`. Use the `User.findByPk()` method along with the `userId` route parameter. Fetch the correct user from the database and return it as a JSON response.
1. Don't forget to `try/catch` any errors.
1. Make some HTTP requests to this new end-point. Try requests with existing user ID's and non-existent ID's. Confirm that both return status `200`.
1. Since we should return a `404` when we do not find a requested resource, adjust your code accordingly.
1. Try fetching non-existing users again, and confirm that you now see a `404` status code.

If you are stuck, or want to see a completed solution, look at [this file](https://github.com/Codaisseur/course-content-exercises/blob/master/week-5/sequelize-day2-day3/index.js).

# Updating a user

Updating a user in a RESTful Web Service means handling `PUT` requests to `/users/:userId`. For this we are going to need some `id` and the new `user` data. The former should come from the route parameter, while the latter should be the request body. The update is a two step process. First we load the user, and then (if it exists), we update it. It's possible to update the row without first loading it into memory. However, we need/want the full object, so we can return it to the user.

The Sequelize code to update a user looks like this:

```javascript
app.put("/users/:userId", async (req, res, next) => {
  try {
    const userId = parseInt(req.params.userId);
    const userToUpdate = await User.findByPk(userId);
    if (!userToUpdate) {
      res.status(404).send("User not found");
    } else {
      const updatedUser = await userToUpdate.update(req.body);
      res.json(updatedUser);
    }
  } catch (e) {
    next(e);
  }
});
```

Play around with it and confirm we can update existing users and that we get a 404 response when updating a non-existant user.

## Exercise: Implement the "create" and "update" actions on todoLists

1. Continue with the Node.js project from the previous exercise.
1. Import the `todoList` model.
1. Implement a `GET` endpoint for all `todoLists`.
1. Implement the `POST` and `PUT` handlers for the `todoLists`, use the ones we defined for `user` as a starting point. The endpoints could be `/todoLists` and `todoLists/:listId` for example.
1. Try them out. Remember, to create todoLists we are going to have to send a valid `userId` in the request.

If you are stuck, or want to see a completed solution, look at [this file](https://github.com/Codaisseur/course-content-exercises/blob/master/week-5/sequelize-day2-day3/index.js).

# Read a user's list

Great, now we have access to the `todoLists` as well. What if we wanted to get all the list for one user? We could build an endpoint on the `/todoLists` resource that filters by `userId`. But, as we know this two resources are related, a more restful way of doing this would be creating and endpoint that searches for `lists` _inside_ a user, using the associations we established. This would be something like this:

```javascript
app.get("/users/:userId/lists", async (req, res, next) => {
  try {
    const userId = parseInt(req.params.userId);
    const user = await User.findByPk(userId, {
      include: [TodoList],
    });
    if (user) {
      res.send(user.TodoLists);
    } else {
      res.status(404).send("User not found");
    }
  } catch (e) {
    next(e);
  }
});
```

## Exercise: Implement all other routes

1. Continue with the Node.js project from the previous exercise.
1. Use the previous snippet to implement the "read a single list" end-point.
1. Implement the route to _create_ a list for a user on the `/users/:userId/lists` endpoint. What HTTP method and URI should it respond to? Use the `TodoList.create` method, like we did for users. Additionally, it would be nice to validate/confirm that a task created for a non-existent user results in a `404`.
1. Implement the rest of the routes:

   ```javascript
   // Update an existing list
   app.put("/users/:userId/lists/:listId", (req, res, next) => {
     /*..*/
   });
   // Delete a user's list
   app.delete("/users/:userId/lists/:listId", (req, res, next) => {
     /*..*/
   });
   // Delete all user's lists
   app.delete("/users/:userId/lists", (req, res, next) => {
     /*..*/
   });
   ```

   _Hint:_ The objects returned by query methods have a method `.destroy()` which can be used to delete them.

1. Test each end-point using an HTTP client. Make sure all of them handle errors (using the built-in error handler), and return `404` when appropriate.
1. When a `DELETE` request is handled successfully, you can send back a `204` status, which means "No Content". This is appropriate for empty responses.

If you are stuck, or want to see a completed solution, look at [this file](https://github.com/Codaisseur/course-content-exercises/blob/master/week-5/sequelize-day2-day3/index.js).
