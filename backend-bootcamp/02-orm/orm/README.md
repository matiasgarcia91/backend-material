---
title: Object-Relational Mapping
tags:
  - Databases
  - SQL

new_terminology:
  - "Object-Relational Mapping (ORM)"
  - "Entity/Model"

learning_goals:
  - id: object_relational_mapping
    description: |
      You are able to explain how an ORM uses object-relational mapping to connect object (oriented) code to a relational database.
  - id: orm_risks_benefits
    description: |
      You are able to list some risks and benefits of using an orm.
---

# Object-Relational Mapping

<LearningGoals />

# What's the problem?

Working with objects (as most programming languages do) is fundamentally different from tables in a relational database. Your database "thinks in tables". Most programming languages "think in objects". In other words, the two have a communication issue. Take this example object:

```javascript
const myObject = {
  name: "My Object"
};
myObject.other = myObject;
```

This object has a reference to itself on one of its properties. Assuming that this is crucial information, how do we store it? How do we make sure the object looks the same after we've loaded it. Object references in our code do not work with IDs (by default). For such references to be persisted, we need to generate IDs. Object identity is an interesting topic:

```javascript
const foo1 = {
  id: 1
};
const foo2 = {
  id: 1
};
foo1 !== foo2; // These are two different objects, despite the id properties being equal.
```

As we know, variables refer to values. We can use these variable to retrieve data and we can check whether two variables point to the same data. But in tables we use primary keys to identify the records. The primary keys are guaranteed to be unique. There are no references in relational databases _other_ than keys. Comparisons like in the previous snippet simply do no exist in databases.

There are many decisions to make when mapping from objects to a relational model. Manual object-relational mapping is dull and repetitive work. A prime candidate for automation. Enter the ORM library.

<MultiChoice
id="1563373660097"
question="How do databases check if two object references are equal?"
options={[
"Databases don't have objects, let alone _multiple_ object references",
"They put primary keys on each object and keys are always unique"
]}
shuffle={true}
correctIndex={0}
/>

# How to use ORMs

ORM libraries automatically map objects to and from tables. Each library has its own way of configuring this mapping. Each _type_ of object is called an **entity** or a **model**. This should be familiar terminology.

In general, ORM libraries need two things from us:

1. Describe the mapping between objects and tables
1. Info on how to connect to the database. Most ORMs support a wide range of different databases.

In return, we get:

1. Convenient methods to query and manipulate the database
1. The ability to create our database tables _from_ our mappings. This is a common feature of ORM libraries.

The following are some hypothetical scenarios. This is not functioning code, but simply an illustration of what ORM libraries let us do. Let's start with an object in memory.

```javascript
const myObj = {
  // 1.
  name: "John Smith", // 2.
  firstName: "John",
  lastName: "Smith", // 3.
  address: {
    // 4.
    street: "Fairway dr.",
    number: "5B"
    // 5.
  }
};
```

Corresponding to the numbers in the above snippet:

1. To be stored, this object needs a primary key. Otherwise we cannot identify it.
2. What should be the data type of the name column? This is a question we need to consider carefully when creating a table.
3. To normalize, we probably shouldn't store the `name` property, but calculate it on-demand with string concatenation.
4. Is the address an entity to be stored in its own table? or should the main table have a `address_street` and `address_number` column?
5. In memory, at any point, data can be missing (`undefined` or `null`). Is this object complete? Are there more fields? Should we allow the columns to be nullable (meaning: to contain `null` values)?

Now, consider this _pseudo-code_. In it, we've made some choices to address these questions.

```javascript
// Although this example is very similar to that of a real
// ORM library, it is NOT REAL CODE!!!

const User = define({
  // A model with an implicit ID/primary key
  firstName: Types.TEXT, // mapping properties to column types
  lastName: Types.TEXT
});
// A separate entity/table to store addresses
const Address = define({
  street: Types.TEXT,
  number: Types.INTEGER,
  // an optional/nullable column
  zip: { type: Types.INTEGER, nullable: true },
  city: Types.TEXT,
  country: Types.TEXT
});
// Create a relationship between the two tables.
// Defines a column user_id in the Address table.
Address.belongsTo(User);
```

The models we define must map JavaScript types (like strings and numbers) to SQL types (like `VARCHAR`, `TEXT` or `INTEGER`). They also can contain extra constraints which are not immediately available in the JavaScript language. One such example is telling the ORM library that a column should contain only unique values.

<MultiChoice
id="1563373660098"
question="ORM model should match the database table's structure"
options={[
"True",
"False"
]}
shuffle={true}
correctIndex={0}
/>

<MultiChoice
id="1563373660099"
question="ORM libraries let us add database constraints to our model"
options={[
"True",
"False"
]}
shuffle={true}
correctIndex={0}
/>

Now that we've defined a model, ORM libraries will provide convenient ways to read rows from a table and return them as an array of objects. Such as in this snippet:

```javascript
const users = User.findAll();
const usersNamedJohn = User.findAll({ where: { firstName: "John" } });
const userByIdIncludingAddress = User.findByPk(5, { include: [Address] });
```

As you can see, this code does not contain any SQL. Instead, it's a query language made of JavaScript methods and configuration objects. We can specify `where` clauses, load rows by primary key (pk), and include related entities.

> Note: The code examples in this module are written in a similar style to the Sequelize ORM library. However, this code is _not_ meant to be used directly, and serves merely as an example.

# Risks of using an ORM

Modeling relationships directly in SQL is more involved than letting the ORM library do it for you. Also, joining tables to automatically load related rows is handled transparently by the ORM. As a result, it can be easy to lose sight of the performance of the SQL queries. It's a common issue. Often, applications that perform well in development start to slow down drastically after deployment due to inefficient, complex queries. During development there are fewer users and much less data.

To prevent this, awareness is key. Avoid using **eager** fetching. This is something you can opt-in to with most ORM libraries. Eagerly-loaded relationships are relationships that are _always_ loaded. Sometimes you don't need the related entity. It's also possible that a related entity itself also has an eagerly fetched relationship. So, you may end up inadvertently joining many tables at once.

To keep your database access fast, make sure you limit how much data is fetched. Only fetch what you need. Whether this means avoiding unnecessary joins, or limiting the maximum number of returned records (pagination), using an ORM does not absolve you from these responsibilities.

<MultiChoice
id="1563373660100"
question="ORM libraries can actually _create_ our tables for us, using our model."
options={[
"True",
"False"
]}
shuffle={true}
correctIndex={0}
/>

<MultiChoice
id="1563373660101"
question="ORM libraries always generate optimal SQL queries"
options={[
"False",
"True"
]}
shuffle={true}
correctIndex={0}
/>

<MultiChoice
id="1563373660102"
question="Each database type has its own ORM libraries"
options={[
"No, most ORM libraries support multiple databases.",
"Yes, we have to use the ORM written for our database."
]}
shuffle={true}
correctIndex={0}
/>
