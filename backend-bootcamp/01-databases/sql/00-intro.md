---
title: Intro to SQL
---

# Intro to SQL
SQL (Structured Query Language) is a language used to query (i.e. "retrieve data from"), as well as update relational databases. SQL queries specify which columns, rows and tables to select and return. It also supports transformations (like mathematical expressions) and aggregations (like sums and averages).

SQL has been standardized and many databases support the standard. However, they each add extra functionality on top of standard SQL. This means that, although most of SQL can be used without knowing about the underlying technology, for the last 5% we still have to be aware of vendor-specific additions to the language. This guide assumes the use of **PostgreSQL 9.6** for any vendor-specific features.

The query language is made up of different types of statements, and can be grouped into two categories:

1. Statements that _change_ or _update_ the database.

    - `INSERT` - SQL statement to insert new rows.
    - `UPDATE` - SQL statement to update zero or more rows.
    - `DELETE` - SQL statement to delete zero or more rows.
    - `CREATE TABLE` - SQL statement to create a new table.
    - `DROP TABLE` - SQL statement to drop a table (i.e. delete the table and all data in it).
    - `ALTER TABLE` - SQL statement to alter/update the structure of a table (i.e. which columns the table has, and of what types they are).

2. The `SELECT` statement that _retrieves_ data from the database according to our given criteria. Most of your efforts will go into learning how to use this `SELECT` statement, and the various _clauses_ it has in order to retrieve specific data, filter the resulting rows, combine data from multiple tables, order the data, aggregate it, etc.:

    - `SELECT` - SQL _query_ statement to retrieve data from the database
      - `WHERE` - SQL query clause to filter which rows are returned
      - `ORDER BY` - SQL query clause to order the resulting rows
      - `JOIN ... ON ...` - SQL query clause to include/combine data from another table
      - `GROUP BY` - SQL query clause to _aggregate_ the resulting data

    This querying part of the language SQL is quite different from usual "imperative" programming languages like JavaScript. It *describes how to select data* from tables in a high-level way, instead of having you explicitly "handle and manipulate the data", say in for-loops (such languages are called "declarative" because, in contrast to imperative ones, they are used to express *what* to do, not *how* to do it). You can best think of this in terms of a _pipeline of data (transformation)_.
    
    If you're familiar with `map`/`filter`/`reduce`, then you can use that kind of thinking as an analogy when learning SQL. Both in order to learn SQL, as well as to solidify your understanding of `map`/`filter`/`reduce`. But keep in mind: _this is just an analogy, because these are still entirely different technologies._
