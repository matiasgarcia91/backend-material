---
title: Transactions and Migrations
---

# Transactions

As the database holds all our application's data we have to prevent errors from causing inconsistencies in our data. While writing backend code it's important to always think how to maintain a valid database state. What is a valid state? Let's look at it through an example:

Let's take a standard money transfer from John to Bob. First you subtract €50 from the John's bank account and you add that same €50 to Bob's bank account. In code, that would be:

```js
async function moneyTransfer() {
  try {
    const john = await User.find(1);
    const bob = await User.find(2);

    await john.update({ bank_account: john.bank_account - 50 });
    await bob.update({ bank_account: bob.bank_account + 50 });
  } catch (e) {
    console.error(e);
  }
}
```

Simple, right? However...

Database queries may produce errors. Maybe the database connection dropped, or something unexpectedly went wrong with the query, your database server is overloaded.... In that case we could fall into a state where we got the money from John, but never added it to Bob's account.

We can solve this only if we can make sure both queries are either run at the same time, or not at all. That all-or-nothing concept is called atomicity.

To make this an atomic operation, we must combine the two queries into a single `Transaction`.

In sequelize this is easily done by calling `sequelize.transaction()`. An example of this would be:

```js
async function moneyTransfer() {
  try {
    const john = await User.find(1);
    const bob = await User.find(2);

    await sequelize.transaction(async t => {
      await john.update({ bank_account: john.bank_account - 50 }, { transaction: t });
      await bob.update({ bank_account: bob.bank_account + 50 }, { transaction: t });
    });
  } catch (e) {
    console.error(e);
  }
}
```

Note we hand the transaction object `t` to each of the queries. We've now solved the money transfer problem. If any of the queries fail neither will be applied and our data will remain consistent. We'll look at an example of implementing this further on.

# Migrations

Just like the requirements and features of your project will change over time, so too will the database model have to change. For the scope of the exercises we are handling this seems easy, just update de database, right? Well, if you're on a team of developers, everyone probably runs de app locally in their own machines. On top of that there might be a testing enviroment running in Heroku with another copy of the database. How do we keep all those databases updated to work for the latest version of our code?

The solution to this problem is the concept of `database migrations`. Migrations provide a way to keep track of the changes to our database model, just like git does for our code! They store instructions on what must be executed on our database to update it, as well as to revert the process (`up`/`down`) just like the `seed` files.

# Migrations in Sequelize

Let's go back to our exercise from last section. We're going to generate a migration to alter our `TodoItem` to contain a new boolean attribute: `important`.

The first thing we have to do is alter the `TodoItem` model definition with our new attributes. Open the `models/todoItem.js` file and add this new attribute as the others are, with the dataType `BOOLEAN` like:

```js
important: DataTypes.BOOLEAN;
```

Now, to generate the migration's skeleton run:

```bash
$ npx sequelize-cli migration:generate --name first-migration
```

And open the new file inside `migrations/`. You'll have a skeleton file just like the ones that are generated for seeds. To perform the operation we need we are going to use another method of `queryInterface`: `addColumn`. The queryInterface object holds many useful methods for this type of operations, for a full list you can take a look a the [documentation](https://sequelize.org/master/class/lib/query-interface.js~QueryInterface.html)

`addColumn` takes 4 arguments:

- Model name
- New column name
- New column configs (type, unique, allowNull, ...)
- Optional query configs

Our migration file should end up looking like this:

```js
"use strict";

module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.addColumn("todoItems", "important", { type: Sequelize.BOOLEAN }, {});
  },

  down: async (queryInterface, Sequelize) => {
    await queryInterface.removeColumn("todoItems", "important", {});
  },
};
```

Let's try it out! Go to your terminal and run

```bash
$ npx sequelize-cli db:migrate
```

You should see a success message like:
![first-migration](https://p98.f4.n0.cdn.getcloudapp.com/items/bLuGpgZo/Image+2019-12-11+at+4.16.55+PM.png?v=43cc2c275138ce04f824d0e39db488b1)

Now, thanks to the having created three models, we have already 4 migrations in our folder! Let's explore a bit this time-travelling power migrations give us over our databases.

Open up your database client and check the `important` attribute has been added to the `todoItems` table. Switch back to your terminal and run:

```bash
$ npx sequelize-cli db:migrate:undo
```

Now refresh your table, the new column is gone, isn't it? Well, `undo` by itself will take us back only one step, the last one. If we call it again we'll revert one more step. But we can also tell the command where exactly in time we want to go. This is done by passing a specific migration file's name to it, like:

```bash
$ npx sequelize-cli db:migrate:undo:all --to 20191212131657-create-todo-list
```

Try it out and see the changes in your client. Finally, we can use `db:migrate:undo:all` to back to the very start of our database's story. Run it and check in your client that all tables have disappeared.

The situation we are on now is exactly the same as when you're going to run your app in a new machine. Well, we have the database created but that's just running `npx sequelize-cli db:create` after setting up the config file.

Now, thanks to having `Migrations` we can simply set up the database according to our version of the code by running:

```bash
$ npx sequelize-cli db:migrate
```

And all our migrations will be exectued in order leaving our database set up with the schema our code requires.

![ALL-MIGRATIONS](https://p98.f4.n0.cdn.getcloudapp.com/items/geurropJ/Image+2019-12-12+at+3.07.52+PM.png?v=2d66df29e1ad72652bca8566f7913bdf)

# Extra: Implement transactions in migrations

Let's investigate a bit how we could use `Transactions` in practice. To try this out we are going to try to add two columns to one of our tables, making one of the column inserts fail on purpose and seeing how `sequelize` handles this. To begin:

1. Generate a new migration `npx sequelize-cli migration:generate --name transactions-test`
1. Set up the migration to add two columns to our `users` table, `city` and `street`.
1. For the `street` column we are going to declare an invalid dataType (on purpose). Your migration file should look something like this:

```js
"use strict";

module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.addColumn("users", "city", { type: Sequelize.STRING }, {});
    await queryInterface.addColumn("users", "street", { type: Sequelize.ANYTHING }, {});
  },

  down: async (queryInterface, Sequelize) => {
    await queryInterface.removeColumn("users", "city", {});
    await queryInterface.removeColumn("users", "street", {});
  },
};
```

1. Run the migration (`npx sequelize-cli db:migrate`), you'll get and error but ignore it and check in your database client what happened.
1. We got the `city` column to be added but not the `street` one right? Of course this is obvious since we defined it with a nonsense type, but what if we wanted either both columns or none of them? Here is where `transaction` are going to help us, let's rewrite that migration to work as we want it to.
1. Before, undo the migration: `npx sequelize-cli db:migrate:undo`. You'll notice that `sequelize` will not undo our latest migration as it considers that it failed, but the new column is still there! What a mess! This is one of the reasons we have to be careful when writing migrations.
1. Let's go back to the start and write this migration in the right way. Run `npx sequelize-cli db:migrate:undo`.
1. Rewrite the migration using transactions. It will end up like this:

```js
"use strict";

module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.sequelize.transaction(async t => {
      await queryInterface.addColumn(
        "users",
        "city",
        { type: Sequelize.STRING },
        { transaction: t }
      );
      await queryInterface.addColumn(
        "users",
        "street",
        { type: Sequelize.ANYTHING },
        { transaction: t }
      );
    });
  },

  down: async (queryInterface, Sequelize) => {
    await queryInterface.sequelize.transaction(async t => {
      await queryInterface.removeColumn("users", "city", { transaction: t });
      await queryInterface.removeColumn("users", "street", { transaction: t });
    });
  },
};
```

1. Now, run all migrations. We'll get the same error but check your database. No extra column! This is the `transaction` we defined in action. As one of the columns was flawed, the transaction took care that none of the changes were applied.
