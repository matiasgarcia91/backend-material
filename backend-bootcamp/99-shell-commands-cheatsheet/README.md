---
title: Shell Commands Cheatsheet
description: |
  A list of our most used terminal commands
tags:
  - Docker
  - Sequelize
  - HTTPie
---

### Advanced Bootcamp Shell Commands Cheatsheet:

# HTTPie

```shell
# GET request to localhost:4000
$ http :4000/

# POST request to localhost:4000 with body
$ http POST :4000/ name=bobby
# or, if you are sending a body HTTPie will default to a POST request
# so you can skip the `POST` parameter like:
$ http :4000/ name=bobby.

```

# Docker

```shell
# Get an overview of all the commands you can use
# (Like this cheatsheet, but way more stuff)
$ docker

# This command lists the running containers.
$ docker ps

# This command lists all containers, both running and stopped.
$ docker ps -a

# Stop and remove stopped containers.
$ docker stop <name|id>
$ docker rm <name|id>

# This command let's us set enviroment variables inside the container.
$ docker run -e SOME_DATA="this is some data" my-image

# Start up postgres image and set POSTGRES_PASSWORD env variable.
$ docker run -d -p 5432:5432 -e POSTGRES_PASSWORD=secret postgres
```

# Sequelize

```shell
=======
# Get an overview of all the commands you can use
% (Like this cheatsheet)
$ npx sequelize-cli

# Initialize a new sequelize project.
$ npx sequelize-cli init

# Create database after setting config.json
$ npx sequelize-cli db:create

# Create database after setting config.json
$ npx sequelize-cli db:drop

# Generate a new model
$ $ npx sequelize-cli model:generate --name user --attributes email:string,phone:integer

# Generate a new seed file
$ npx sequelize-cli seed:generate --name some-users

# Running and undoing seeds.
$ npx sequelize-cli db:seed:all
$ npx sequelize-cli db:seed:undo:all

# We can also point to a specific seed file to run instead of "all" using the --seed flag
$ npx sequelize-cli db:seed --seed 20191211110453-some-users
$ npx sequelize-cli db:seed:undo --seed 20191211110453-some-users

# Generate a new migration file
$ npx sequelize-cli migration:generate --name my-test-migration

# Run migrations.
$ npx sequelize-cli db:migrate

# Undo last or all migrations
$ npx sequelize-cli db:migrate:undo
$ npx sequelize-cli db:migrate:undo:all

# Undo until a certain migration.
$ npx sequelize-cli db:migrate:undo:all --to 20191212131657-create-todo-list

# Useful combination if something goes wrong while migrating or seeding and we have no real data
$ npx sequelize-cli db:drop
$ npx sequelize-cli db:create
$ npx sequelize-cli db:migrate
$ npx sequelize-cli db:seed:all
```
