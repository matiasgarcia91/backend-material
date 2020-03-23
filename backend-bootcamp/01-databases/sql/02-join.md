---
title: Joining tables
---

# Joining tables

Well-designed databases store every "entity type" in its own table. This usually means having many different tables in a database, say `products`, or `users`, `teams`, `cars`, `dinosaurs`, `likes`, `planets`... etc.

To combine data from multiple tables, you could run multiple queries separately, but most often you will want to use joins. With a `JOIN` statement, you can combine data from multiple tables into a _single query_.

Let's create tables for users and teams, and _relate_ (or associate) these tables. (You'll probably want to _drop_ the `users` table from the previous section.)

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  first_name TEXT,
  last_name TEXT,
  team_id INT
);

CREATE TABLE teams (
  id SERIAL PRIMARY KEY,
  color TEXT,
  points INT
);

INSERT INTO teams
  (id, color, points)
VALUES
  (1, 'Red', 100),
  (2, 'Green', 200);

INSERT INTO users
  (first_name, last_name, team_id)
VALUES
  ('Floor', 'Visser', 1),
  ('Floor', 'Valk',  2),
  ('Floris', 'Vos', 1),
  ('Fatima', 'Voorthuizen', 1);
```

Let's imagine we want to show something like this in an app we're building:

Name               | Team color
---                | ---
Floor Visser       | Red
Floor Valk         | Green
Floris Vos         | Red
Fatima Voorthuizen | Red

One possible (but _bad_) way to achieve this, would be to:

1. First, select all the users in our database with an SQL query like `SELECT * FROM users`.
2. Then, loop over the resulting data with a `map` expression or `for` loop, and for each of these users, perform an additional SQL query like `SELECT color FROM team WHERE id = (the team id in question)`. For example, assuming we have some SQL-querying library function `performSQLQuery`, this could look like:

    ```js
    const users = performSQLQuery(`SELECT * FROM users;`);
    for (let user of users) {
      const { color } = performSQLQuery(`SELECT color FROM teams WHERE id = ${user.team_id}`);
    }
    ```

The reason this would be a _bad_ idea is twofold. First of all, because we need to perform many (many) SQL queries. In geek terms this bad practice is called the `N+1` query, because we need `N+1` queries if there are `N` users. The second reason is because of the fact that, as a rule, databases are considered _very fast_, whereas programming languages (comparatively) _quite slow_. So that means that we've doing the "hard lifting work" in the slower technology: JavaScript.

**Instead**, we can just `JOIN` the information from the other table:

```sql
SELECT *
FROM users
JOIN teams ON teams.id = users.team_id;
```

This is very cool! We add the data from an extra table using `JOIN`, using an _equation_ (the `teams.id = users.team_id` part) to express the relationship between the two tables.

If you run this query, you'll find out that you get back all the fields of both tables. That's more than we need. Precious bytes sent over the wire, which could have been avoided.

Let's just select the specific fields we're interested in having:

```sql
SELECT users.first_name, users.last_name, teams.color
FROM users
JOIN teams ON teams.id = users.team_id;
```

This returns only the data that we need!

<MultiChoice
  id="1563881363371"
  question="By analogy, which FP-style JavaScript expression would this last SQL query most resemble?"
  options={[
    <Markdown>
    ```js.slim
    users
      .map(user => ({ user, team: teams.find(team => team.id === user.team_id) }))
      .map(({ user, team }) => ({ first_name: user.first_name, last_name: user.last_name, color: team.color }))
    ```
    </Markdown>,
    <Markdown>
    ```js.slim
    users
      .map(user => ({ first_name: user.first_name, last_name: user.last_name }))
    ```
    </Markdown>,
    <Markdown>
    ```js.slim
    teams
      .map(team => ({ team, user: users.find(user => team.id === user.team_id) }))
      .map(({ user, team }) => ({ first_name: user.first_name, last_name: user.last_name, color: team.color }))
    ```
    </Markdown>
  ]}
  singleColumn
  shuffle
  correctIndex={0}
/>

Alternatively we can also give the tables a short alias using `AS`:

```sql
SELECT u.first_name, u.last_name, t.color
FROM users AS u
JOIN teams AS t ON t.id = u.team_id;
```

Setting up table aliases is so common, that the keyword `AS` is in fact completely unnecessary. This example does the exact same thing:

```sql
SELECT u.first_name, u.last_name, t.color
FROM users u
JOIN teams t ON t.id = u.team_id;
```

The following example lists the tables separated by commas, and puts the join condition inside the `WHERE`-clause. 

```sql
SELECT u.first_name, u.last_name, t.color
FROM users u, teams t
WHERE t.id = u.team_id;
```

Most people find this syntax less readable. It makes it less explicit what the relationship is between the tables (`t.id = u.team_id`) because it's just part of the normal WHERE.

## Exercise: Join some tables

Use this dataset:

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP,
  name TEXT,
  description TEXT,
  price DECIMAL(12,2),
  category_id INT
);

CREATE TABLE categories (
  id SERIAL PRIMARY KEY,
  name TEXT,
  image TEXT
);

INSERT INTO categories
  (id, name, image)
VALUES
  (1, 'Green products', 'https://cd.sseu.re/greenProduct.png'),
  (2, 'Non-green products', 'https://cd.sseu.re/nonGreenProduct.jpeg');

INSERT INTO products
  (created_at, name, description, price, category_id)
VALUES
  ('2017-01-01', 'Hammer', 'This green hammer is amazing for sustainable businesses that want to make a dent in the universe', 10, 1),
  ('2017-02-02', 'Hammer', 'This red hammer is amazing for people that just don''t like another color' , 12.4, 2),
  ('2017-06-06', 'Xylophone', 'If you like things that start with an X, buy this', 10, 2),
  ('2017-05-05', 'LP of John Lenon', 'This totally green piece of history is pure perfection', 10, 1),
  ('2017-04-04', 'Linkin Park CD', 'Sometimes it doesn''t even matter, as long as it''s Green (with a capital g)', 9.67, 1);
```

Write queries that return:

1. All products, including the `name` and `image` of the category the product is in
2. Only the products, including category information which price is greater than or equal to 9.9 EUR
