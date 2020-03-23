---
title: Advanced APIs
description: |
  Today we'll go deeper into the concept of APIs. We touch on middlewares, authentication, and express routers.
sections:
  - 01-middlewares
  - 02-router-class
  - 03-security
tags:
  - Express
  - APIs
  - Security
quiz: https://codaisseur.typeform.com/to/iUjFWj
new_terminology:
  - "API"
  - "Authentication"
  - "Pagination"

learning_goals:
  - id: registration_authentication
    description: |
      You are able to set up user registration & authentication for your back-end.
  - id: pagination
    description: |
      You are able to apply pagination in a realistic API.
---

# Advanced APIs

First, you will learn about how to use the `Router` class to divide your express app into multiple files and also how to create `middlewares` for your servers.

Then we will dive into authentication and pagination, while practicing our ORM skills and building an imageboard server.
You will use Sequelize to model images and users.
Relations will allow you to connect these data types together to form useful entities.

## Today's Goals

- learn how to extend express with routers in separate files
- learn how to create APIs which implement user authentication and use middlewares
