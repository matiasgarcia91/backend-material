---
title: Other types of JOINs
---

# Other types of JOINs

We've covered joins in which case there was 1 row related with another row. If either of the rows did not exist (e.g. a user without a team) the row would __not__ be part of the result-set. There are other variants of joins that can be useful in some cases.

## LEFT JOIN

Let's say you have some users and the users can create a profile if they want to. That means not all users have a profile. And let's assume this is how you've setup your database:

```sql
-- Make sure to drop your table before creating...
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  first_name TEXT,
  last_name TEXT
);

CREATE TABLE profiles (
  id SERIAL PRIMARY KEY,
  user_id INT,
  upvotes INT,
  content TEXT
);

INSERT INTO users
  (id, first_name, last_name)
VALUES
  (1, 'Floor', 'Visser'),
  (2, 'Fleur', 'Valk'),
  (3, 'Floris', 'Vos');

INSERT INTO profiles
  (upvotes, user_id, content)
VALUES
  (100, 1, 'Floor is the best'),
  (200, 2, 'Look at my Insta pics -x- Fleur');
```

What if we want to have the user with the content of their profile? We can't just do a join:

```sql
SELECT users.first_name, users.last_name, profiles.content
FROM users
JOIN profiles ON profiles.user_id=users.id;
```

Wondering why not? If you try the above, you'll find the query will only return for those users that __have__ a profile, not the ones who do not have a profile.

Internally, by default, a `JOIN` will do an `INNER JOIN`. That means, it will only return data for entries that exist in the left table (left of the `JOIN` keyword) **and** that exist in the right table (right of the `JOIN` keyword).

If we want all users regardless of a profile entry, but __if__ they have a profile we also want to query that, we have to explicitly use a `LEFT JOIN`. It works very similar to a join:

```sql
SELECT users.first_name, users.last_name, profiles.content
FROM users
LEFT JOIN profiles ON profiles.user_id=users.id;
```

This will return three rows, and for one of which the content field will be `null`.

## Exercise: DIY

Given the following dataset, complete the assignments:

```sql
CREATE TABLE news_articles (
  id SERIAL PRIMARY KEY,
  journalist_id INT,
  subject TEXT,
  content TEXT,
  publish_date TIMESTAMP
);

CREATE TABLE images (
  id SERIAL PRIMARY KEY,
  url TEXT,
  news_article_id INT
);

CREATE TABLE journalists (
  id SERIAL PRIMARY KEY,
  first_name TEXT,
  last_name TEXT
);

INSERT INTO news_articles (journalist_id, subject, content, publish_date) VALUES
  (2, 'Monkeys escaped from the zoo', 'It really happened', NOW()),
  (4, 'Crocodiles escaped from the zoo', 'It really happened, it was scary', NOW()),
  (6, 'Meerkats escaped from the zoo', 'It really happened. It was funny.', NOW()),
  (2, 'Foxes escaped from the zoo', 'It really happened', NOW()),
  (4, 'Snakes escaped from the zoo', 'It really happened. They took a plane.', NOW());

INSERT INTO images (url, news_article_id) VALUES
  ('https://cd.sseu.re/Schermafbeelding_2018-03-14_om_20.10.32.png', 2),
  ('https://cd.sseu.re/Schermafbeelding_2018-03-14_om_20.11.13.png', 3),
  ('https://cd.sseu.re/Schermafbeelding_2018-03-14_om_20.11.46.png', 1),
  ('https://cd.sseu.re/Schermafbeelding_2018-03-14_om_20.12.14.png', 4),
  ('https://cd.sseu.re/Schermafbeelding_2018-03-14_om_20.12.50.png', 5);

INSERT INTO journalists (first_name, last_name) VALUES
  ('Rabbit', 'the Rabbit'),
  ('Rattlesnake', 'the Rattlesnake'),
  ('Raven', 'the Raven'),
  ('Rhinoceros', 'the Rhinoceros'),
  ('Rat', 'the Rat'),
  ('Raccoon', 'the Raccoon');
```

Write queries to retrieve the following:

1. All news articles with the image that is connected to the article
1. All journalists, even if they never wrote an article, including the number of articles (use `count(id)` for this)

> ## 🙄 Joins, joins joins
>
> There are very many different joins, for as many different use cases.
>
> In the image below, `A` is the LEFT table, `B` is the RIGHT.
![joins joins joins](https://cd.sseu.re/SQL_Joins.svg)
