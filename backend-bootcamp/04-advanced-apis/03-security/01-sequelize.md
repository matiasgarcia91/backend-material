---
title: Setup and models
---

# Sequelize

Setup your database using sequelize-cli:

1. Install `sequelize`, `sequelize-cli` and `pg`.
1. Initialize your sequelize project, remember to do this run:

```bash
$ npx sequelize-cli init
```

1. Check that you have your Postgres Docker running (`$ docker ps`) and it's port (`5432` by default). If this is not the case remember the command to start it is:

```shell
$ docker run -d -p 5432:5432 -e POSTGRES_PASSWORD=secret postgres
```

1. Open up the `config/config.json` file and configure your development database. Remember it should en up looking something like this:

```json
"development": {
    "username": "postgres",
    "password": "secret",
    "database": "imageboard-database",
    "host": "localhost",
    "dialect": "postgres",
    "operatorsAliases": false
  },
```

1. Run `sequelize-cli db:create` to make sure everything is set up correctly.

# Generate Models

We are going to generate two models, one to store the images and another one for the users. Use

```bash
$ npx sequelize-cli model:generate --name {modelName} --attributes attr1:{TYPE},attr2:{TYPE},...
```

to generate these two models with the following attributes:

- `image`: title and url, both `string`.
- `user`: email, password and fullName; all `string`.

Open up the `user` model and let's add some constraints to our attributes to make our app more robust. We do not want to accept `NULL` values for any of the three attributes and also, `email` should be unique in our table.

```js
"use strict";
module.exports = (sequelize, DataTypes) => {
  const user = sequelize.define(
    "user",
    {
      email: { type: DataTypes.STRING, allowNull: false, unique: true },
      password: { type: DataTypes.STRING, allowNull: false },
      fullName: { type: DataTypes.STRING, allowNull: false },
    },
    {}
  );
  user.associate = function(models) {
    // associations can be defined here
  };
  return user;
};
```

You also need to add these constraints in the migration file, so these validations are applied at DB level. If you like you can also add constraints to the `image` attributes.

We'll take care of adding the relation between the models later, for the security and authorizations aspects we want to focus on today we don't need to worry about it.

Good, let's create our tables. Run:

```bash
$ npx sequelize-cli db:migrate
```

and if no errors are thrown open up your DB client and check how the tables look.

Awesome! Let's commit all our changes and go on into setting up some routes.
