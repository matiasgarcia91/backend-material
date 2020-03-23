---
title: Querying Relations
---

# Seeding our Relations

Let's get some test data into our database so we can start trying out our newly defined relations and understanding the power behind them. We already know how to get test data into our database, using `seeds`. As you are continuing yesterdays project you probably already have seed data in your app. If not, generate some for each of the models. Remember, the command was:

```bash
$ npx sequelize-cli seed:generate --name dummy-users
```

As we have just established relations in our app we are going to have to add some attributes to our seed data. To make sure all our `ids` match, let's first clear the database by doing:

```bash
$ npx sequelize-cli db:drop
$ npx sequelize-cli db:create
$ npx sequelize-cli db:migrate
```

Now, in our seed for `todoLists` we are going to add values for the new `userId` column, in this fashion:

```js
"use strict";

module.exports = {
  up: async (queryInterface, Sequelize) => {
    return await queryInterface.bulkInsert(
      "todoLists",
      [
        {
          name: "George's Work list",
          userId: 1,
          createdAt: new Date(),
          updatedAt: new Date(),
        },
        {
          name: "George's Personal list",
          userId: 1,
          createdAt: new Date(),
          updatedAt: new Date(),
        },
        {
          name: "Leo's futbol list",
          userId: 2,
          createdAt: new Date(),
          updatedAt: new Date(),
        },
      ],
      {}
    );
  },

  down: async (queryInterface, Sequelize) => {
    return await queryInterface.bulkDelete("todoLists", null, {});
  },
};
```

Do the same for `todoItems` giving them id's to match the lists. Seeds get their ids in the order they are defined in the seed file (if we have a clean DB).

Now that we have relations sequelize is going to check on insert if we entered valid values for the foreign keys in the table, in this case our `userId`. Run the command and make sure you don't get any errors.

```bash
$ npx sequelize-cli db:seed:all
```

# Querying Relations

Now that our data has associations we can include them in our queries, making them much more powerful and helping us avoid having to do several queries sequentially. Create a new file in your project root and name it `relation-queries.js`.

For a first example we can try getting our TodoLists with some information about the user who owns them. For this we are going to use the `include` keyword in our query, like this:

```js
const { user, todoItem, todoList } = require("./models");

async function listsWithUsers() {
  const lists = await todoList.findAll({
    include: [user],
  });

  return lists.map(list => list.get({ plain: true }));
}

listsWithUsers().then(lists => console.log(lists));
```

`include` can take and array of models and will return the associated rows inside the result of what you're querying for, in this case `todoLists`. `include` can also take an array of objects, which can hold all the same properties as the ones we give the `findAll()` for example. If we wanted to only include the name of our `Users` with the lists we could change the function above to:

```js
async function listsWithUsers() {
  const lists = await todoList.findAll({
    include: [{ model: user, attributes: ["name"] }],
  });
  return lists.map(list => list.get({ plain: true }));
}
```

The `todoList` table is the one holding the `userId`, but that doesn't mean we can't turn this query around by getting users with their lists. Sequelize will still understand the association so we could do:

```js
async function getUsers() {
  const allUsers = await user.findAll({
    include: { model: todoList, attributes: ["name"] },
  });
  return allUsers.map(user => user.get({ plain: true }));
}

getUsers().then(users => console.log(users));
```

And we'll get inside each user a `todoList` key holding an array of the user's lists. Pretty neat right? Try them out!

See if you can build queries to achive the following:

1. Get one user by `id` with his lists.
1. Get `important` TodoItems with the `name` of the list they belong to.
1. Get one user by `id` with his lists, which also contain their belonging TodoItem's `task` attribute.

Examples of these queries and the rest of the TodoListApp we have been building so far can be found [here](https://github.com/Codaisseur/course-content-exercises/tree/master/week-5/sequelize-day2-day3)
