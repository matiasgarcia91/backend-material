---
title: PostgreSQL

new_terminology:
  - postgres

learning_goals:
  - id: run_postgres_in_docker
    description: |
      You know how to run PostgreSQL on your machine using Docker

  - id: connect_to_postgres_with_gui
    description: |
      You know how to connect to and use PostgreSQL from a GUI

tags:
  - Postgres
  - Docker
---

# PostgreSQL
PostgreSQL (a.k.a. postgres) is a popular relational database. After using it through a GUI, you should have a better feeling for how it all fits together.

<LearningGoals />

# Running PostgreSQL with Docker
To run postgres in a container and connect to it, you can run the following command:

```shell
$ docker run -d -p 5432:5432 -e POSTGRES_PASSWORD=secret postgres
```

Since you don't need to see the output of the database process, running in the background (the `-d` option) is recommended. The default port for postgres is `5432`. You can map it to whichever port you like on the host, but if you're only going to have one postgres container running, then using the default is recommended. The container is configured to use the `POSTGRES_PASSWORD` environment (specified by the `-e` option) variable as the default user password. Rest assured, since the database runs on your local machine, the password is not sensitive information. You can name the container with the `--name` option, for a convenient reference.

# Using a database GUI
Graphical user interfaces are usually more intuitive than a text-based interface. We recommend you install either [QueryPIE](https://www.querypie.com/), or [Postico](https://eggerapps.at/postico/) (Mac-only), or else [DBeaver](https://dbeaver.io/download/). QueryPie and Postico have beautifully straightforward interfaces. DBeaver can be a bit more overwhelming. So [here's a video tutorial we've prepared](https://www.youtube.com/watch?v=clgTYIpFEYs). These GUIs take some getting used to, here are two important tips to ease the learning curve:

1. After certain changes, especially creating and dropping tables, you'll need to refresh the UI. Otherwise, the changes will not appear, despite having completed successfully.
1. When creating SQL statements in the GUIs, you can type several of them separated by semicolons. There will be options to run either all, or only the currently selected statement.


# Exercise: Connect and execute
1. Run PostgreSQL on your machine, in a Docker container.
1. Configure your database GUI of choice to connect to your postgres instance. Use the following info:
   - The host name should be `localhost` (since it's running on your own machine)
   - The port is whatever you mapped it to in the `docker run` command. If you followed our recommendation, it will be `5432`.
   - The username to log-in with is `postgres`. This is the default.
   - The password is whatever you specified as the value of the `POSTGRES_PASSWORD` environment variable in the `docker run` command.
1. Using your GUI, try executing the following statement
   ```sql
   SELECT NOW();
   ```

# Quiz

<MultiChoice
  id="postgres_docker_container__what_is_POSTGRES_PASSWORD_used_for"
  question="What should be the value of `POSTGRES_PASSWORD` when starting the postgres container?"
  options={[
    "It has to be equal to the value in your client's connection configuration",
    "It has to be set to `secret`",
    "It has to match the password in the postgres documentation"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="postgres_docker_container__how_to_map_ports"
  question="To publish postgres container's port and make it available to the outside world, what command-line option would work?"
  options={[
    "`-p 8888:5432`",
    "`-p 5432:1234`",
    "`-p 5433:5433`"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="postgres_docker_container__a_gui_is_a_db_client"
  question="A database graphical user interface like Postico or DBeaver acts as what?"
  options={[
    "a database client",
    "a database server",
    "a database abstraction layer"
  ]}
  shuffle={true}
  correctIndex={0}
/>
