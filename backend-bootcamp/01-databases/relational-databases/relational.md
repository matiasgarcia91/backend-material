---
title: "Relational databases"
---

# Relational databases

Databases *abstract away* (or "hide") the details of how data is stored, and provide an *interface* through which you manage and query data. This way, you don't have to be concerned about the complex details of storing data efficiently.

By far the most well-known type of databases is called the **relational database**, invented in 1970. A database that is built on this model is called a "Relational Database Management System" (RDBMS). In the same year the *language* **"Structured Query Language" (SQL)** was introduced. This language is the *interface* by which you can "talk" to these kinds of databases. SQL is like a programming language, except that it is much more focused on achieving one particular task, namely: searching and manipulating data in a database.

Although the name is a bit lengthy, relational databases are actually quite simple: they consist of *tables* of data. Tables are like spreadsheets. A table called "teachers" might look like this:

| id | name  | role               | hair_color        | alive |
|----|-------|--------------------|-------------------|-------|
| 1  | Plato | king philosopher   | NULL              | false |
| 2  | Obama | epic president     | becoming greyish  | true  |
| 3  | Rein  | Codaisseur teacher | brown             | true  |

The *structure* of this table is defined by its five columns: (1) an integer column "id" that is used to uniquely identify a certain teacher (also known as a **primary key**), (2) a textual column denoting a teacher's name, (3) a textual column to denote that teacher's role, (4) a textual column that denotes that teacher's hair color, and (5) a boolean column recording that teacher's current state of existence. (And apparently, cells of the hair color column can be "undefined" (NULL): of course we don't know what Plato's hair color was.)

Note that each *row* of the table denotes a single teacher. All teachers are alike, in the sense that they share the same structure, defined by the table. Where each row has to be of the same shape (i.e. table rows are *homogeneous*), each row on its own can have *heterogeneous attributes*, i.e. of different types. The table can be said to represent an entity (a real-world concept), and the rows represent instances of that entity.

A relational database typically contains multiple tables. In addition to teachers, we might also have a table "sayings" like so:

| id | teacher_id | saying                                                                                       |
|----|------------|----------------------------------------------------------------------------------------------|
| 1  | 1          | Love is a serious mental disease.                                                            |
| 2  | 2          | Yes we can!                                                                                  |
| 3  | 2          | The future rewards those who press on.                                                       |
| 4  | 2          | Whatever we think of the past, we must not be prisoners to it.                               |
| 5  | 3          | Writing good Git commit messages helps future employers know that you're a good team-player. |
| 6  | 1          | Wise men talk because they have something to say; fools, because they have to say something. |

Interestingly, this table *references* the "teachers" table. This way of referencing is typical of relational databases. If a table references another table's primary key, that is known as a **foreign key**. As a small leap ahead (you don't have to understand how this code works just yet), let's look at a typical *SQL query* that we could issue to the database, in order to retrieve all sayings of dead teachers:

```sql
SELECT teachers.name, sayings.saying
  FROM teachers
  JOIN sayings
    ON sayings.teacher_id = teachers.id
 WHERE teachers.alive = false;
```

This query, when issued to the database, would result in the following data:

| name  | saying                                                                                       |
|-------|----------------------------------------------------------------------------------------------|
| Plato | Love is a serious mental disease.                                                            |
| Plato | Wise men talk because they have something to say; fools, because they have to say something. |

Note how the resulting data is also in *tabular format*. The rule of thumb is that "everything in relational databases revolves around tables". You store your data in tables, and results of queries are also structured like tables (except they're not stored).

Summarizing:

- Relational databases are a kind of database, that stores data in tables;
- In these tables, each column has a *name* and a *type* (e.g. integer, string, boolean), and denotes an *attribute* of the *entity* that is to be stored in the table;
- A table represent a single abstract *entity*, a "thing in the world" if you will. A row represents a concrete instance of that entity. 
- Every row has the same shape, or structure, which is defined by the table it is in, also called a *schema*.
