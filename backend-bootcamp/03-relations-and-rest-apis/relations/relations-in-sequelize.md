---
title: Relations in Sequelize
---

# Relations in Sequelize

> Important: For todays topics we are going to continue building up on yesterdays To-Do List app. If you didn't finish or weren't here for the day, here is a starter app you can clone for today: https://github.com/Codaisseur/course-content-exercises/tree/master/week-5/day3-starter-kit

Now that we know a bit about relation/associations and how to think about them let's take a look into how to implement these with Sequelize, once again using migrations.

First let's take a moment to think about our structure, for that it's useful to take another look into the simple UML we have for our TodoListApp.

![TODO-APP-UML](https://p98.f4.n0.cdn.getcloudapp.com/items/yAuLY6XB/Image+2019-12-17+at+3.04.42+PM.png?v=a54d5f0eeb46816496e7fec899389aff)

From this diagram we can extract the following relationships:

1. A `User` can have many (or none) `TodoLists`, but one `TodoList` belongs to one `User`.
1. A `TodoList` can have one or more `TodoItems` and `TodoItems` must belong to a `TodoList`.

Sequelize facilitates a bunch of methods to specify these relationships. Some of them are:

- `belongsTo()`
- `belongsToMany()`
- `hasOne()`
- `hasMany()`

There are more but these are the basic ones we'll be using over and over again. For a full list you can check out the [documentation](https://sequelize.org/master/manual/associations.html).

Using this basic "building blocks" we are going to declare the relationships we have between our models. Following the reasoning we extracted from the diagram these would be:

1. TodoList `belongsTo()` User, User `hasMany()` TodoLists
1. TodoItem `belongsTo()` TodoList, TodoList `hasMany()` TodoItems.

If we wanted to restrict the system so that `Users` can only have one `TodoList` we would change the second declaration on point 1. from `hasMany()` to `hasOne()`.

# Migrations and declarations in models

The first step to implementing this is to generate a new migration, to do so remember we run:

```bash
$ npx sequelize-cli migration:generate --name set-up-relations
```

We are going to use this migration to add the necessary columns to our models in order to tie our data together as we want to. One important thing to remember when establishing associations is that it's not enough with just adding the necessary columns in, we also have to specify them in our models! This way sequelize can know how the models are related and provide us with help later on when querying our related data. Let's begin with the `users` => `todoList` relation.

First open both model files. In our `User` file we have to add a `hasMany()` relation. To do this we add to our model definition:

```js
user.associate = function(models) {
  user.hasMany(models.todoList);
};
```

So that it ends up something like:

```js
"use strict";
module.exports = (sequelize, DataTypes) => {
  const user = sequelize.define(
    "user",
    {
      name: DataTypes.STRING,
      email: { type: DataTypes.STRING, unique: true },
      phone: DataTypes.INTEGER,
    },
    {}
  );
  user.associate = function(models) {
    user.hasMany(models.todoList);
  };
  return user;
};
```

Great! Now onto the `todoList` model. Here we have to do a very similar change but using `belongsTo()` instead of `hasMany()`, this way:

```js
todoList.associate = function(models) {
  todoList.belongsTo(models.user);
};
```

Great! Models are done! Now back to our new migration file and let's declare those columns! We can see we are going to need a `userId` column in our `todoList` table, to tie lists to users. Remember how we added new columns in migrations? Well, this is going to be really similar but we are going to pass some extra information to `addColumn` so that sequelize understands that it's not just any type of column but one that creates a relationship.

```js
"use strict";

module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.addColumn("todoLists", "userId", {
      type: Sequelize.INTEGER,
      references: {
        model: "users",
        key: "id",
      },
      onUpdate: "CASCADE",
      onDelete: "SET NULL",
    });
  },
  down: async (queryInterface, Sequelize) => {
    await queryInterface.removeColumn("todoLists", "userId");
  },
};
```

According to the Sequelize docs:

> Creating an association will add a foreign key constraint to the attributes. All associations use CASCADE on update and SET NULL on delete, except for n:m (belongsToMany()), which also uses CASCADE on delete.

These are strategies on how to handle manipulating these references when updating or deleting records, so we are going to set them according to the docs.

Let's run our migration and see if everything goes smoothly, remember:

```bash
$ npx sequelize-cli db:migrate
```

If no errors are displayed (there shouldn't), you can use your database client to check if the `userId` column was correctly added to our `todoLists` table. Congrats! You've just established your first association! We still have to add the `TodoList` -> `TodoItem` relation. To keep things tidy and avoid generating more migrations we are going to use the same one we just used to do both associations in one go. First, let's undo this last migration so we can modify it again.

```bash
$ npx sequelize-cli db:migrate:undo
```

# DIY

1. In the same migration file, add a new column to `todoItems` with a reference to `todoLists`.
1. Add the necessary associations to both models.
1. Run migration and check everything went well. You should end up with a `userId` column in `todoLists` table and a `todoListId` column in the `todoItems` table.
