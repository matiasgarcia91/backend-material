---
title: Databases
description: |
  Databases are an important part of any web app. Let's discover a bit more about them.
sections:
  - containers
  - docker
  - postgres
  - relational-databases
  - sql
  - entity-modelling
tags:
  - SQL
  - Databases
  - Docker
---

<!-- old quiz: https://codaisseur.typeform.com/to/iydpHs -->

# Databases

Databases are at the heart of pretty much any non-trivial web application nowadays. Whether it's an enterprise bank or a blog about your backpacking trip in Australia, your app needs a database. They provide us the tools to save (**persist**) our application's data. This makes it quite important; if doesn't work or isn't available, good chances your app won't work.

Today we'll learn a bit more about databases, what they are and why we need them, how to structure them, and how to work with them.

# Containers

Processes can run inside containers. So, a container can be seen as a closed environment for processes. It (partially) isolates them from the (operating) system they're running on. A "contained" process may have restricted access to the file-system and hardware. It's like a computer inside your computer. This concept is also known as virtualization. The file-system inside a container might be virtual (meaning: not "real").

This provides several advantages for us developers, including benefits to security, making our programs more portable and the running and deploying of them programs more predictable (we can re-create the exact enviorment we need for them to run).

# SQL

The most standard language for databases since 1970, SQL has been around for a long time. But it is not all history, SQL is still one of the core techonologies used today to develop backend software meaning that we are probably going to stumble upon it whether we jump into an existing project or start something from scratch!
