---
title: Relations and REST APIs

description: |
  Yesterday we took a look at how using ORMs can make our lives simpler as back-end developers. Today we are going to introduce the concept of relations, what we use them for and how we can set them up. After that we are going to take a look at how we can build our first simple but fully functional REST API.

sections:
  - relations
  - web-services
  - rest-in-express

tags:
  - Relations
  - Express
  - APIs
new_terminology:
  - "Relataions"
  - "Cardinality"
  - "API"
  - "REST"
  - "CRUD"

learning_goals:
  - id: relations
    description: |
      You can explain how relations work and can specify which relations are more appropiate for different data models.
  - id: api
    description: |
      You are able to explain what an api is.
  - id: rest_api_technologies
    description: |
      You are able to build a simple restful CRUD api using ExpressJS, Docker, Postgres & Sequelize.
  - id: understand_rest
    description: |
      You are able to describe the main characteristics of a REST api.
  - id: use_sequelize
    description: |
      You can use sequelize to perform all CRUD actions in your database.
---

# Extending our backend knowledge:

This last two days we learned how to set up databases and how to use an ORM to define models which in turn will be translated into DB tables by our ORM. Today we are going to take that knowledge further, learning how to set up relations between out entitites and also how to set up a simple express server with routes (an API) to get data in and out of our backend!

🙋 An API is an **Application Programming Interface**. You build an API to give your front end access to a server.

Visually, that would look like:
![Server](https://miro.medium.com/max/2000/1*OcmVkcsM5BWRHrg8GC17iw.png)

- src: [What exactly is an API - Perry Eising](https://medium.com/@perrysetgo/what-exactly-is-an-api-69f36968a41f)

When we talk about _the back end_, we usually mean the combination of an API, a server and a database.
You'll learn more about API's later today, in the `web-services`-module.

<LearningGoals />
