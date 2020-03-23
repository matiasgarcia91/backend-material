---
title: Security
description: |
  Today you'll learn how to control who sees what data.
sections:
  - 01-sequelize
  - 02-rest
  - 03-pagination
  - 04-jwt
  - 05-hashing-passwords
  - 07-login
  - 08-auth-middleware

tags:
  - Security
quiz: https://codaisseur.typeform.com/to/iUjFWj
---

# Security

To learn about security, authentication and the usage of JWT tokens in the backend we are going to build a imageboard app, where we can view and post images.

You will:

1. Model image posts in a postgresql database.
1. Access a database with sequelize and a REST API.
1. Keep some data private and require users to signup and login before contributing.

Initialize the project:

1. Add a new repository in your GitHub named `imageboard-server`.
1. Make a new directory `imageboard-server`.
1. Initialize with `npm init -y` and `git init`.
1. Add a `.gitignore` file that includes the line `node_modules/`.
1. Add the GitHub repository as a `remote origin` and push what you have.
1. Install `express`.
1. Inside an `index.js` file:
   1. Require `express`.
   1. Create a new express server named `app`.
   1. Declare a constant `port` equal to `process.env.PORT` or 4000.
   1. Call `app.listen` with the `PORT` and callback function.
1. Start the server.

Test the server in your browser.
You should see `Cannot GET /` in the browser and a message like `Listening on :4000` in your terminal.

Great! If everything is working let's continue onto setting up our Sequelize backend.
