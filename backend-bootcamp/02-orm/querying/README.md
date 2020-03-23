---
title: Querying
---

# Querying

There are several query methods on your model that will allow you to find one or more rows in your tables. Each of these returns a promise. Here are some examples:

```javascript
try {
  // Select all rows. Resolves with a (possibly empty) array
  const allUsers = await User.findAll();
  // Select all rows where firstName === 'Dave', but only return the first one.
  // Resolves with an object or undefined (if no matching rows exist)
  const specificUser = await User.findOne({ where: { name: "Dave" } });
  // Select a row by its primary key. Resolves with an object or undefined (if no matching rows exist)
  const userByPk = await User.findByPk(3);
  // A query using a numeric operator
  const Op = Sequelize.Op;
  const tallUsers = await User.findAll({
    // WHERE height >= 175
    where: {
      height: {
        [Op.gte]: 175, // gte stands for 'greater than or equal'
      },
    },
  });
} catch (error) {
  console.log(error);
}
```

Other operators and querying options are documented [here](http://docs.sequelizejs.com/manual/querying.html#where).
To use this operators we must also import them from sequelize using

```js
const { Op } = require("sequelize");
```

A common use-case is to take a long list of results and allow users to step through them page by page. We can limit the number of results, but it's also useful to know how many results there _would have been_. So, we not only want part of the results, but also a count of all results for our current query. Here is how you do that:

```javascript
try {
  const result = await Project.findAndCountAll({
    where: {
      title: {
        [Sequelize.Op.like]: "foo%",
      },
    },
    offset: 10,
    limit: 2,
  });

  console.log(result.count);
  console.log(result.rows);
} catch (e) {
  console.error(e);
}
```

The `offset` option tells Sequelize where in the list of results you want to start, while `limit` tells it how many rows (maximum) should be returned. The promise resolves with an object containing the array of results (`rows`) and the `count`. This process is called **pagination**.

## Common mistakes!!!

These issues come up often:

1. A query method like `findAll` and `findOne`, like any SQL query, might find _nothing_ (zero results)! This is not an error! Therefore the promise resolves successfully. The code in your `catch` rejection handler will only be called for problems like broken connections, syntax errors, or constraints violations.
1. The objects constructed by Sequelize have methods on them like `save`, `update` and `destroy`. Also, there are a lot of properties that we're not really interested in. If you want to log stuff to the console, the structure is a little messy. [As the docs say](http://docs.sequelizejs.com/manual/instances.html#values-of-an-instance):
   > If you log an instance you will notice, that there is a lot of additional stuff. In order to hide such stuff and reduce it to the very interesting information, you can use the `get`-attribute. Calling it with the option `plain = true` will only return the attributes of an instance.
   >
   > ```javascript
   > try {
   >   const john = await Person.create({
   >     name: "Rambow",
   >     firstname: "John",
   >   });
   >
   >   console.log(john.get({ plain: true }));
   > } catch (e) {
   >   console.error(e);
   > }
   >
   > // result:
   > // { name: 'Rambow',
   > //   firstname: 'John',
   > //   id: 1,
   > //   createdAt: Tue, 01 May 2012 19:12:16 GMT,
   > //   updatedAt: Tue, 01 May 2012 19:12:16 GMT
   > // }
   > ```

## Exercise: Search for ToDo Items

1. Create a new `sample-data.js` file in the root of our `TodoList` project.
1. We are not going to do this through `seeds` so we can take the oportunity to also try out the `.create` method we are regularly going to use in our routes. Take the following code snippet:

   ```javascript
   const TodoItem = require("./models").todoItem;

   async function createSampleTodoItems() {
     try {
       const todo1 = await TodoItem.create({
         task: "Clean bedroom",
         important: false,
       });
       const todo2 = await TodoItem.create({
         task: "Learn to code",
         important: true,
       });
       const todo3 = await TodoItem.create({
         task: "Have fun",
         important: true,
       });

       return [todo1, todo2, todo3].map(item => item.get({ plain: true }));
     } catch (e) {
       console.error(e);
     }
   }

   createSampleTodoItems().then(todos => console.log(todos));
   ```

1. Run the code with `node sample-data.js` to confirm that three rows are inserted.
1. Taking this file as an example, create a second file called `queries.js` and try to make functions which:
   - Searches for all `users` and logs them.
   - Searches for all TodoItems and logs them (use `plain: true`).
   - Searches for a `user` by primary key.
   - Creates a new `user`. (Once you manage to create this user, delete or comment the function call as to no run it again, if not we we'll get an error).
   - Searches only for `important` `TodoItems`

A finished example implementation can be found [here](https://github.com/Codaisseur/course-content-exercises/blob/master/week-5/sequelize-day2-day3/queries.js).
