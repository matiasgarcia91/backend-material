---
title: "Quiz"
---

# Quiz

<MultiChoice
  id="sql__when_two_rows_match_inner_join"
  question="For an `INNER JOIN`, what happens when the `ON` condition is true for a pair of rows?"
  options={[
    "The two rows appear in the results as a single row",
    "The two rows appear in the results as separate rows",
    "The row from the left table is included in the results"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="sql__when_two_rows_match_left_join"
  question="For a `LEFT JOIN`, what happens when the `ON` condition is true for a pair of rows?"
  options={[
    "The two rows appear in the results as a single row",
    "The two rows appear in the results as separate rows",
    "The row from the left table is included in the results"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="sql__when_two_rows_do_not_match_left_join"
  question="For a `LEFT JOIN`, what happens when the `ON` condition is _false_ for a pair of rows?"
  options={[
    "The row from the left table is included in the results regardless of matching rows in the right-side table",
    "The two rows appear in the results as a single row",
    "The two rows appear in the results as separate rows"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="sql__inner_join_matching_rows_can_appear_multiple_times"
  question="When performing an `INNER JOIN`, how many times can a row's values appear in the results?"
  options={[
    "As many times as there are matching pairs",
    "At most once",
    "Exactly once"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="sql__what_is_groub_by"
  question="What does it mean when you `GROUP BY` a column?"
  options={[
    "Rows that share the same value for that column will be grouped into one",
    "That column's values can be aggregated using functions like `SUM`, and `AVG`",
    "Results will be ordered by that column and grouped into sets of rows"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="sql__what_is_dropping_a_table"
  question="What does it mean to `DROP` a table?"
  options={[
    "The whole table is deleted",
    "All the table's rows are deleted",
    "It removes constraints (keys) from the table"
  ]}
  shuffle={true}
  correctIndex={0}
/>
