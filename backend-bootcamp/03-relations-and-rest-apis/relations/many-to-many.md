---
title: Many to Many Relationships
---

## Many-To-Many

These type of relationships require a few extra steps to set up. Let's take the example of a new entity `Tags` which we can apply to our TodoItems. An item can have more than one tag but we are not going to define tags multiple times. We are going to have a `Tags` table where we create them with a `title` property.

To make the association we are going to need whats is know as a `join table`. This is a table which is going to tie together TodoItems to Tags. Let's extend our UML to incorporate these changes.

![TODO_APP_WITH_TAGS](https://p98.f4.n0.cdn.getcloudapp.com/items/4gumPyxJ/Basic+TodoList+UML.png?v=d8f169b80999926a15f424f4343e2dd2)

Let's break what we have to do to accomplish this into steps:

1. Generate model for `Tag` and `ItemTags`.
1. Modify the generated migration file for `ItemTags` to reference the other two tables.
1. Define the relation on the `Tag` and `TodoItem` model using the `through` keyword.

Let's begin by generating the models

```shell
$ npx sequelize-cli model:generate --name tag --attributes title:string
$ npx sequelize-cli model:generate --name itemTag --attributes todoItemId:integer,tagId:integer
```

Now, as mentioned before, to establish this relationships we need to do a bit more tweaking of our files. What we need to do is make those two attributes we defined for the `itemTag` model (`todoItemId`, `tagId`) actually point to the corresponding models. To do that we need to open the generated migration for the `itemTag` model and add some key/values to the field definition object. Open that file and let's take a look.

This is how the file should look:

```js
return queryInterface.createTable("itemTags", {
  id: {
    allowNull: false,
    autoIncrement: true,
    primaryKey: true,
    type: Sequelize.INTEGER,
  },
  todoItemId: {
    type: Sequelize.INTEGER,
  },
  tagId: {
    type: Sequelize.INTEGER,
  },
  createdAt: {
    allowNull: false,
    type: Sequelize.DATE,
  },
  updatedAt: {
    allowNull: false,
    type: Sequelize.DATE,
  },
});
```

So for each of those two attributes we need to add the reference and the update/delete strategies below the `type` key. So:

```js

// From:
todoItemId: {
  type: Sequelize.INTEGER,
},

// To:
todoItemId: {
  type: Sequelize.INTEGER,
  references: {
    model: "todoItems",
    key: "id"
  },
  onUpdate: "CASCADE",
  onDelete: "CASCADE"
},
```

And the same for the `tagId` column.

The next (and final!) step is to specify the relations/associations in the model files. Those can be split into two groups, the relationship between the models we want to associate and the connections to the `join table`: `itemTags`:

Join table to entities:

- itemTag --> `belongsTo` --> tag
- itemTag --> `belongsTo` --> todoItem

Entities amongst themselves:

- todoItem --> `belongsToMany` --> tag
- tag --> `belongsToMany` --> todoItem

For this second group we are going to also need to tell sequelize that this relationship is achieved `through` the `itemTags` table.

The first group are the same as the relations we already set up so go ahead and open the `itemTag` model and write them down.
The second set of relations are going to look like this:

```js
tag.belongsToMany(models.todoItem, {
  through: "itemTags",
  foreignKey: "tagId",
});
```

Go ahead and set them on both model files, don't forget to change the model and foreign key values when adding it to the `todoItem` model.

Run `db:migrate` command too make sure we don't get any errors!

If everything goes fine let's generate some new seed data so we can try our new `Tags`

1. Generate two new seed files, `some-tags` and `some-tag-items`.
2. Create some dummy data for the `Tags`.
3. Do the same for `itemTags` in the `some-tag-items` file, these will be `{ todoItemId, tagId }` pairs (with createdAt, updatedAt attributes as well), where we effectivly adding tags manually to our todoItems.

> Before running these seeds it might be good to run `db:drop` and `db:migrate`. If you already had some seed data in your db the `ids` will probably be mismatched. Do this only when playing around with seed/dummy data, `db:drop` will delete everything and we'll lose real data if we had any.

Run seeds and if you get no error let's try and query out new relation!

1. Open up the `relation-queries.js` file again.
1. Import our new `tag` model at the top
1. add a query that finds all todoItems with their corresponding tags.

A finished example for all we've been doing up to now can be found [here](https://github.com/Codaisseur/course-content-exercises/tree/master/week-5/sequelize-day2-day3)
