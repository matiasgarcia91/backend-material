---
title: "ORMs"

description: |
  This week we start with the backend world of web development. That means we'll first
  dive into tools we can use to create and manipulate our database.

sections:
  - orm
  - sequelize
  - models-and-seeds
  - transactions-and-migrations
  - querying

tags:
  - SQL
  - ORMs
  - Sequelize

new_terminology:
  - "Postgres"
  - "Sequelize"

learning_goals:
  - id: understand_orms
    description: |
      You can explain what ORMs are and why we use them.
  - id: set_up_sequelize
    description: |
      You are able to set up sequelize in your project and connect it to a database.
  - id: sequelize_models_and_migrations
    description: |
      You can define Models and use migrations to create the corresponding tables in your database.
  - id: sequelize_seeds
    description: You can set up your database with dummy data using seeds.
  - id: use_sequelize_query
    description: |
      You can use sequelize to perform queries in your database.
  - id: pagination_in_sequelize
    description: |
      You are able to use pagination to limit the number of results to fixed-size pages.
---

# First steps in the back-end

Last weeks you learned a lot about the front end. This is that part of your application that runs on the client's browser. The front end is important, because without it your clients don't know what to do and how to do it.

There is a limit to what you can do in the front end of your app. **Generally if you want to store things you need to do that in the backend**. If you have a webshop you need a place to store your products and the stock, and that should not be lost when you close your browser window.

This week we'll be looking at the back end of web applications. The back end is the server-side of things; that part of your application that runs on a webserver. It's very important that you realize where some code is running while you're writing it. To visualize the front end and back end side of your web app:

![Back end Front end](https://cd.sseu.re/backend-frontend.png)

> 🙋To summarize: You will need to build a server if...:
>
> - ...you want to prevent losing data when the user refreshes the page. _(For example by storing data in a database)_
> - ...you want to make sure that some code does not show up in the source code of your front end - because everyone can see it! _(Think about privacy sensitive information, validations, game logic, etc.)_

<LearningGoals />
