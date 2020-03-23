---
title: "Webshop Server Project"
tags:
  - Sequelize
---

# Project briefing: building the backend for a webshop

Last week you worked on building a webshop front-end using React and Redux. This week we are going to build the backend side of this app.

## What are we building?

Using our new knowledge on servers, databases and the Sequelize library we are going to create a backend service for a webshop. We are going to have create and use entities and relations to build our data model. On top of that we also have to build a coherent REST api to expose our backend to the world!

## Common entities in a webshop backend

- Product (name, description, price, image_url, ...)
- Category (name, image_url, description, ...)
- Customer (first_name, last_name, address, email, phone, ...)
- Order (this one is up to you to figure out)

## Learning Objectives

- Setting up models and appropiate relations
- Using migrations for model creations/alterations
- Seeding your db with sample data for your webshop
- Setting up routes to `GET` your resources and to `POST` (create) resources.

## Getting started

**Planning**

(take some time to brainstorm)

1. Get some paper and think about the table structure your backend is going to use.
1. Try to create a UML where you can see the models and their attributes as well as the relationships between the entities.
1. Remember you can also start thinking on what routes your API is going to need.

**The setup**

1. Create a new folder and inside it a new npm project using `npm init -y`
1. Initialize a git repository `git init` and create a `.gitignore` file
1. Install `express`, `pg`, `sequelize` and `sequelize-cli`.
1. Initialize a new sequelize structure using `npx sequelize-cli init`.
1. Start your postgres instance on Docker

```shell
$ docker run -d -p 5432:5432 -e POSTGRES_PASSWORD=secret postgres
```

1. Tweak the `config/config.json` "development" key with your postgres credentials.
1. Run the db:create command to make sure the database connection is working. You should see a message similar to this:

```shell
Sequelize CLI [Node: 10.17.0, CLI: 5.5.1, ORM: 5.21.3]

Loaded configuration file "config/config.json".
Using environment "development".
(node:67336) [SEQUELIZE0004] DeprecationWarning: A boolean value was passed to options.operatorsAliases. This is a no-op with v5 and should be removed.
Database database_development created.
```

1. When all the setup is done do an inital commit.
1. Create your GH repository and push your project's initial commit.
1. Switch to a new branch before starting development.
