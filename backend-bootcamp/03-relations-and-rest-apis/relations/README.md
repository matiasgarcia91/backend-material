---
title: Relations

sections:
  - relations-in-sequelize
  - querying-relations
  - many-to-many

tags:
  - Relations
  - Sequelize
---

# Relations

Relations allow you connect different types of data. Relations describe how records relate to each other.

> A record is a single set of data in your database, like a row in a table.

Let's take as an example our TodoList App, whenever someone signs up, the app creates a User _record_ in the database by adding a row to the `users` table:

###### Users

| id  | name        | email            | phone     |
| --- | ----------- | ---------------- | --------- |
| 1   | Frank Lloyd | frank@coding.com | 567234567 |

That record represents the new user. If another user signed up, they would get their own row in the table:

| id  | name            | email              | phone      |
| --- | --------------- | ------------------ | ---------- |
| 1   | Frank Lloyd     | frank@coding.com   | 567234567  |
| 2   | Charles Babbage | charles@coding.com | 1010101010 |

After that, the user will surely want to create a `TodoList`, so our app will add a new record in the `todolists` table, with the data for that list:

###### TodoLists

| id  | name         |
| --- | ------------ |
| 1   | Frank's List |

We now need a way to tie this list to our new user. Said in another way, we need to document that this particular list belongs to a particular user. The most common pattern for doing this is using a **foreign key**.

> A _foreign key_ is the id of a related record in another table.

To connect our users to their TodoLists we can add a column to the second model called `user_id`, meaning the `id` of the user this list belongs to:

###### TodoLists

| id  | name         | user_id |
| --- | ------------ | ------- |
| 1   | Frank's List | 1       |

Because the value of `user_id` in the first row is 1, you know it belongs to Frank Lloyd, who has the row with `id` 1 in the users table.

Each row in the `TodoList` table should have a `user_id` that matches the id of the user it belongs to. You could say that the list and the user are now related thanks to this foreign key.

Whenever you record a new todoList, you should also record the user it belongs to by assigning a foreign key:

| id  | name                  | user_id |
| --- | --------------------- | ------- |
| 1   | Frank's List          | 1       |
| 2   | Charles work list     | 2       |
| 3   | Charles personal list | 2       |

Relations allow you to easily connect data of different types. Thanks to the foreign key, you can find all the homes that belong to a user if you know their id.

The user Charles Babbage has the id 2. You could find all of their lists with a single query: SELECT \* FROM todoLists WHERE user_id=2.

Most non-trivial models include relations. Relations describe how multiple records connect to each other to describe an entity.

> An entity is a real thing your database wants to describe.
>
> Entities can be composed of a single record. With the help of relations, entities >can be composed out of multiple related records.

# UML

The UML Class diagram is a graphical notation used to construct and visualize object oriented systems. A class diagram in the Unified Modeling Language (UML) is a type of static structure diagram that describes the structure of a system by showing the system's:

1. Classes
1. Their attributes
1. Operations (or methods)
1. The relationships among objects

We are not going to go deep into the UML language but we are going to use it as a simple way to represent and think about our models and the relationships between them. More specifically we won't be using point 3 in our UML's. Let's begin by taking a look at an example of what a stripped down UML would be for the `TodoListApp` we have been working on in the last sections.

![TODO-APP-UML](https://p98.f4.n0.cdn.getcloudapp.com/items/yAuLY6XB/Image+2019-12-17+at+3.04.42+PM.png?v=a54d5f0eeb46816496e7fec899389aff)

Let's break this up into parts. First, we can notice how every one of the boxes represents one of the models we have defined in our `sequelize` backend.

Inside of each of them we can find the attributes each model has.

And last we can see some _weird_ numbers and letters at the end of each link between our model representations. That is going to represent the **cardinality** of our relationship.

**Cardinality** describe how our records relationships work. Tables in databases can be related in several ways, for example some of these are:

| Relationship                          | Example                     | Left       | Right     |
| ------------------------------------- | --------------------------- | ---------- | --------- |
| One-to-one                            | person ←→ birth certificate | 1          | 1         |
| One-to-one (optional on one side)     | person ←→ driving license   | 1          | 0..1 or ? |
| Many-to-one                           | person ←→ birthplace        | 1..\* or + | 1         |
| Many-to-many (optional on both sides) | person ←→ book              | 0.._ or _  | 0.._ or _ |

Let's go over them quickly:

1. **One-to-one**:

   - Strict: if one record exists it **must** have an associated record on it's related table (example: one person must have a birth certificate and a birth certificate must belong to one person.)
   - Optional on either side: This means if a record exists on one table there **can** exist or not a record in it's related table, but if it exists there must be only one (one person _can_ have a drivers license, but he doesn't _have_ to have one).

1. **Many to one**: If we have a person record then it must be related to **one** birthplace. On the other hand, the same birthplace can be shared by many people (one-to-many)

1. **Many to many**:
   - Strict: one record in a table can be linked to many records in its related table and the same the other way around. A student is signed up for many courses and those courses are related to many other students.
   - Optional on both sides: A record can be related to many or not. A person can own many books, or it can own none. The same book can be owned by many people or by none.
