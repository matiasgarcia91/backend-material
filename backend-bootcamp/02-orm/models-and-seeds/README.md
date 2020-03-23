---
title: Models and Seeds
---

# Models

The equivalent to defining tables in SQL is to define models in ORM libraries. In a model file we specify the models _name_ and its _attributes_. The attributes are going to be mapped into columns in our table. We also need to specify the _type_ of data each attribute is going to hold.

`sequelize-cli` gives us a tool we can run in our terminal to quickly generate a new model. Let's define a `User` model to test this tool and understand it's output. Run the following command:

```bash
$ npx sequelize-cli model:generate --name user --attributes name:string,email:string,phone:integer
```

The output informs us that both a `Model` and a `Migration` was succesfully created. Don't worry about the last one, we'll get into it later, for now let's focus on the model.

![MODEL:GENERATE_OUTPUT](https://p98.f4.n0.cdn.getcloudapp.com/items/04ugkO9x/Image+2019-12-11+at+3.12.26+PM.png?v=7af6a4acc43a7348e6bfa3f8613f33d2)

Go to your `/models` folder and open the new `user.js` file sequelize-cli generated, you should have something like this:

```javascript
"use strict";
module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define(
    "user",
    {
      name: DataTypes.STRING,
      email: DataTypes.STRING,
      phone: DataTypes.INTEGER,
    },
    {}
  );
  User.associate = function(models) {
    // associations can be defined here
  };
  return User;
};
```

Let's break this up into the important parts. To define a model `sequelize.define()` is called. We can see this method takes three arguments:

1. The model name. Sequelize will automatically pluralize this name to name the table, so our table in the database will be called `users`.
1. Model attributes. Here we specify which attributes the model has and their dataType. These will be later mapped into columns in our table.
1. An options object, in this case empty, but one we can use to pass special configurations to our model definition.

When defining attributes in the model definition we can also pass more advanced configurations for each attribute. We could for example, not accept `null` values for the `email` while also forcing this column to only accept unique values, because two people cannot really have the same email address right? This would look like this:

```javascript
email: { type: DataTypes.STRING, allowNull: false, unique: true }
```

> IMPORTANT: Just because we have defined our model doesn't mean Sequelize already created the table in the database. For that we are going to have to run the according migration. Migrations are files which specify changes to the database tables. For now we'll run the one generated with our model and we'll take a look at creating our own migrations further ahead.

So, to actually create our `users` table let's run the related migration. To do so execute in your terminal the following command:

```bash
$ npx sequelize-cli db:migrate
```

To be sure, use your database client to check the table was created correctly. If everything works, time to commit again!

# Seeds

Now that our table is set up we'll go into the next step, getting some data into our `users` table. And in come `Seeds` to help us do so.
Seeding a database is a process in which an initial set of data is provided to a database. It will usually empty whatever was inside and then add the data. It's a nice way to populate our tables with data after we create them. To do so we must first generate the skeleton of a seed file using our CLI. Run:

```bash
$ npx sequelize-cli seed:generate --name some-users
```

And open up the generated file inside the `seeders/` folder.

```js
"use strict";

module.exports = {
  up: (queryInterface, Sequelize) => {
    /*
      Add altering commands here.
      Return a promise to correctly handle asynchronicity.

      Example:
      return queryInterface.bulkInsert('People', [{
        name: 'John Doe',
        isBetaMember: false
      }], {});
    */
  },

  down: (queryInterface, Sequelize) => {
    /*
      Add reverting commands here.
      Return a promise to correctly handle asynchronicity.

      Example:
      return queryInterface.bulkDelete('People', null, {});
    */
  },
};
```

Let's take a moment to understand the structure of this file. We have two `keys` (up, down) which hold functions. These functions are the ones which will specify what will happen when we run the seed (`up`) and what will happen if we revert or undo this action (`down`). Following the example sequelize provides we are going to use `bulkInsert` to add some test users to our table. In the `down` section we will want to revert the database to it's original state, which in this case is an empty Users table, so the example provided is almost spot-on (we need to change the table name). Add some test users like:

```js
"use strict";

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert(
      "users",
      [
        {
          name: "Leo Messi",
          email: "leo@messi.com",
          phone: 1234567,
          createdAt: new Date(),
          updatedAt: new Date(),
        },
        {
          name: "Dan Abramov",
          email: "dan@redux.com",
          phone: 1234567,
          createdAt: new Date(),
          updatedAt: new Date(),
        },
      ],
      {}
    );
  },

  down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete("users", null, {});
  },
};
```

Now we'll run the seed to make sure our table gets some data. To run do:

```bash
$ npx sequelize-cli db:seed:all
## To un-do the seed we can use
$ npx sequelize-cli db:seed:undo:all
## We can also point to a specific seed file to run instead of "all" using the --seed flag
$ npx sequelize-cli db:seed --seed 20191211110453-some-users
$ npx sequelize-cli db:seed:undo --seed 20191211110453-some-users
```

Try it out by going back and forth a few times and checking the changes on the database client. Commit your progress so far.

To end this section, let's set up a simple endpoint to get our data out of the server. We must install express as a dependency and set up one route. Run `npm install express` and create an `index.js` file in the root of your proyect, like this:

```js
const express = require("express");
const User = require("./models").user;
const app = express();
const PORT = 4000;

app.get("/users", async (req, res) => {
  const users = await User.findAll();
  res.send(users);
});

app.listen(PORT, () => console.log(`Server started in port: ${PORT}`));
```

Now try it out! Run `node index.js` and you can use `httpie` or your browser to test this endpoint.

<MultiChoice
id="1563375128975"
question="What does calling `sequelize.define` do?"
options={[
"It defines a single entity model, which maps to a table",
"It defines a database connection",
"It defines a foreign key relationship",
"It defines a database constraint"
]}
shuffle={true}
correctIndex={0}
/>

<MultiChoice
id="15633751264564"
question="What do seeds help us do?"
options={[
"Get initial data into the DB tables",
"Apply changes to the DB tables",
"Define models"
]}
shuffle={true}
correctIndex={0}
/>

# Exercise:

We'll continue using this project in the next sections. Remember to commit after each meaningful step, like we've been doing so far.

1.  Create a new Node.js project and install the following dependencies:
    - `sequelize`
    - `sequelize-cli`
    - `pg`
1.  Run `npx sequelize-cli init`.
1.  Use the same configs for your `config.json` but change the database name to `todo-list-app`.
1.  Generate three models: Users, TodoLists, and TodoItems. Use the following attributes for each:

    - User: name, email, phone.
    - TodoList: name.
    - TodoItem: task, deadline.

Add the corresponding types. Use `STRING` as the dataType for deadline for now.
Don't worry about relations between these models, that is out of scope for this exercise.

1.  Before running `npx sequelize-cli db:create`, open the `User` model file and migration and add the constraint that the email must be `unique`.
1.  Run `npx sequelize-cli db:create` and create your database, check in your database client everything went ok.
1.  If your database is already created tables won't be created for the new models so you have to run the migrations: `npx sequelize-cli db:migrate`
1.  Generate skeleton seed files, one for each.
1.  Get some data into each table.
1.  Play around with applying and reverting the seeds until you feel comfortable with the process
