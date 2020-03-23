---
title: GROUP BY
---

# Grouping

Often you want to "summarize" data in certain ways. For example, you might want to know, for each author on your blog, how many posts they're written; or for each historical period of time, how many kinds of dinosaurs were known to have lived then; or per philosopher, how many books or articles they wrote.

In SQL, we achieve this by "grouping" rows together with a `GROUP BY` clause. Suppose we have a table of blog posts, and in a query we group together rows on the basis of their author being the same. Then, per author, we'd get a single row in our results.

<svg style="width: 100%; height: 200px;" viewBox="-10 -10 620 190">
  <g fill="white" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <!-- left, ungrouped rows -->
    <rect x="0" y="  0" width="200" height="20" stroke="#26a69a" />
    <rect x="0" y=" 30" width="200" height="20" stroke="#26a69a" />
    <rect x="0" y=" 60" width="200" height="20" stroke="#26a69a" />
    <rect x="0" y=" 90" width="200" height="20" stroke="#dc5959" />
    <rect x="0" y="120" width="200" height="20" stroke="#dc5959" />
    <rect x="0" y="150" width="200" height="20" stroke="#444" />
    <!-- right, group rows -->
    <rect x="400" y=" 30" width="200" height="20" stroke="#26a69a" />
    <rect x="400" y="105" width="200" height="20" stroke="#dc5959" />
    <rect x="400" y="150" width="200" height="20" stroke="#444" />
    <!-- grouping arrows -->
    <g>
      <path d="M250  10 Q280 40 350 40
               M250  70 Q280 40 350 40
                 l -8 -7 m 8 7 l -8 7" stroke="#26a69a" //>
      <path d="M250 100 Q280 115 350 115
               M250 130 Q280 115 350 115
                 l -8 -7 m 8 7 l -8 7" stroke="#dc5959" />
      <path d="M250 160 L 350 160
                 l -8 -7 m 8 7 l -8 7" stroke="#444" />
    </g>
  </g>
</svg>

That also means that we need to tell the database how to _aggregate_ (or "combine") these sets of multiple rows into single rows. For this purpose, SQL has aggregation functions that you can use. Some examples:

- By far the easiest to use and most popular: `COUNT`
- Numerical aggregators (but also useful for certain other types of values): `MIN`, `MAX`, `COUNT`, `AVG`, `SUM`
- String aggregators, specific to Postgres: `STRING_AGG`
- Boolean aggregators, specific to Postgres: `BOOL_AND`, `BOOL_OR`
- ...and many more wildly specific ones, often specific to the particular database used. For example, Postgres has a whole slew of aggregators you can use for descriptive statistics.

Let's start with the following data:

```sql
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  created_at DATE,
  title TEXT
);

INSERT INTO posts
  (created_at, title)
VALUES
  (NOW() - interval '4 days', null),
  (NOW(), 'Intro to SQL'),
  (NOW(), 'The COUNT aggregator'),
  (NOW(), 'Why even GROUP in the first place?'),
  (NOW() - interval '1 day', 'How to aggregate strings?'),
  (NOW() - interval '1 day', 'Found a typo in the reader'),
  (NOW() - interval '1 day', 'Analogising SQL with map/filter/reduce'),
  (NOW() - interval '1 day', 'SQL is a wonderful language!'),
  (NOW() - interval '1 day', 'Who needs JavaScript?'),
  (NOW() - interval '4 day', 'Where databases live')
;
```

_Take some time to examine the sophisticated date "math" and the use of the `NOW()` function._

From our posts table, that has a date column that contains the date of creation, you could make a list of posts per day:

```sql
SELECT COUNT(*), created_at
FROM posts
GROUP BY created_at;
```

By analogy, the following FP-style JavaScript in which a `reduce` is used for the aggregation step, would achieve similar results. _You don't need to be able to write this JavaScript, it's purely for demonstrative/comparative purposes. It would be good if you can understand the analogy though._

```js.slim
posts
  .reduce((groups, post) => {
    const group = groups[post.created_at] || [];
    group.push(post);
    return { ...groups, [post.created_at]: group };
  }, {}).entries()
  .map(([created_at, group]) => ({ count: group.length, created_at }))
```

This will print a list of numbers (the `count`) for all the given dates. The way it works is, when you group by a column, rows that have the same value for that column will be returned as a single row. The grouping of multiple rows means that we have the ability to aggregate the values. With `COUNT(*)` we're simply counting the number of rows. There are other aggregate functions like `SUM`, `AVG`, `MIN` and `MAX`. To use these aggregates we'd have to specify what column to "sum" up. Leading to expressions like `MAX(age)` or `SUM(price * value)`.

Lets set up some aliases and sorting:

```sql
SELECT COUNT(id) AS page_count, created_at AS publish_date
FROM posts
GROUP BY publish_date
ORDER BY publish_date;
```

## Exercise: DIY

Given the following dataset:

```sql
CREATE TABLE page_views (
  id SERIAL PRIMARY KEY,
  user_id INT,
  path TEXT,
  date DATE
);

INSERT INTO page_views
  (user_id, path, date)
VALUES
  (1, '/index.html', NOW()),
  (2, '/about.html', NOW()),
  (3, '/index.html', NOW()),
  (4, '/index.html', NOW() - interval '1 day'),
  (5, '/contact.html', NOW() - interval '1 day'),
  (6, '/index.html', NOW() - interval '2 day'),
  (7, '/index.html', NOW() - interval '3 day'),
  (8, '/info.html', NOW() - interval '1 day'),
  (7, '/about.html', NOW() - interval '2 day'),
  (7, '/index.html', NOW() - interval '11 day'),
  (2, '/contact.html', NOW() - interval '3 day'),
  (3, '/info.html', NOW() - interval '1 day'),
  (7, '/contact.html', NOW() - interval '2 day'),
  (8, '/contact.html', NOW() - interval '7 day'),
  (9, '/info.html', NOW() - interval '1 day'),
  (9, '/index.html', NOW() - interval '2 day'),
  (10, '/contact.html', NOW() - interval '3 day'),
  (3, '/index.html', NOW() - interval '10 day');
```

Write queries to retrieve the following:

1. The top 3 most visited pages (e.g. `/index.html`) including the number of visits
2. The number of visits per date (without time) ordered by the date, descending
3. The IDs of the users that made the most page views (most page views first) in the last 5 days
