---
title: Entity Modeling

new_terminology:
  - modeling
  - entity
  - normalization
  - entity relationship
  - database model
  - problem domain
  - entity instance

learning_goals:
  - id: know_entity_relationships
    description: |
      You know what _entity relationships_ are.

  - id: can_map_problem_domain_to_db_model
    description: |
      You know how to map a problem domain to a _database model_.

  - id: know_normalization
    description: |
      You know what _normalization_ is, and what it's good for.
---

# Entity Modeling
While programming, we create models all the time. It's time to get explicit about how we do that, and the trade-offs we make.

<LearningGoals />

## 📚 Extra Resources
These are _not_ required reading/viewing...

1. [This Youtube video](https://www.youtube.com/watch?v=UrYLYV7WSHM) contains a clear (the speaker's accent notwithstanding) explanation of normal forms. **Pro Tip:** Watch it at 1.75x speed.
1. [An article about normal forms](https://www.guru99.com/database-normalization.html)


# Modeling
Modeling is what you do when you want to represent something. A database model represents real things as tables. When we define classes in an OOP program, that's also modeling. You take a concept and choose a structure to represent it. It's important to realize that most physical objects and ideas can be represented in multiple ways, and that the differences can have a lot of impact. Some representations are more practical than others. Which model is "best" depends on what you want to use it for. Because requirements change over time, and often our assumptions turn out to be wrong, there is often no single "best" model.

# Entities
Entities are things in our domain that have an identity. They exist, on their own, independent of other facts. Entities are types, like classes. And just like classes, entities have instances. We see this duality of abstract v.s. concrete all the time. In OOP:

| Abstract  | Concrete |
| ------------- | ------------- |
| class | instance  |
| having a property `name` | actual value "John" |

In relational databases:

| Abstract  | Concrete |
| ------------- | ------------- |
| table | row |
| a column `name` | a cell with value "John" |

In a relational database, entities are tables. The rows of a table represent instances of that entity. The properties of an entity are the columns of the table. The ID (primary key) of the table lets us distinguish instances of that entity.

# Entity relationships
Models are made up of entities **and** the relationships between them. Relationships have a name, and something called "cardinality". The cardinality tells us how many instances can participate on each side of the relationship. For instance, a user in a dating app might have multiple profile pictures, but each profile picture in the dating app's database always belongs to exactly one person. Thus, the cardinality of this relationships is "1 user -to- multiple pictures", or "1:N". In a relational database, relationships between entities are stored as foreign keys.

# Examples
Now that we've briefly defined modeling, entities, and relationships. It's time to look at a couple applications of these concepts. As you read each example, take a minute to really think through the problem, and how _you_ would solve it.

## Personal names
How should you represent a person's name? A single text field might suffice, but if you ever need to separate first and last names, you'd have to convert the existing data, and that might be very difficult. So, usually names are stored in at least two text fields: first and last name. But what about people with multiple first names. Is there a _need_ to distinguish them?

After briefly looking at [this article](https://en.wikipedia.org/wiki/Personal_name). Do you think it's better to use the words "given name" and "surname", than first and last name? Are multiple first names a thing? Or are those "middle names"? Difficult questions, to be sure. The "best" way to represent the data will depend on what your app needs to do. But also who the targer audience is.

## Addresses
Now, a harder one. How should you represent an address? A single text field? Or maybe: street, number, city, zip, and country. If you think this is obvious, then it's time for a [reality check](https://www.mjt.me.uk/posts/falsehoods-programmers-believe-about-addresses/).

It's true that **most** addresses can be represented using a simple model. Perhaps it's a good option to have a model that supports _exceptions_ to the structured address format. For example, a model consisting of: address lines 1 through 3. That way there is a failsafe for people whose addresses fall outside the norm.

## Restaurant reservation
If your problem domain is a restaurant reservation app, an entity might be: a reservation. What about the size of the group (number of people in the reservation)? Why is that not an entity? The "size of the group" cannot exist on its own. It's always associated with a reservation. So, it's a property of the entity. 

A table in the restaurant might also be an entity. It makes sense, because the table exists irrespective of any reservation. On the other hand, you could argue that the table is a property of the reservation. In which case it is not an entity. But there is a trade-off. Before reading on, take a minute to contemplate the consequences of making a reservation's table number a _column_ in the reservation table, or its _own_ table with a _relationship_ to the reservation table.

If our reservation app wants to match reservations to tables, based on the capacity of the table and the size of the group, then we need to store the capacity... somewhere. A table's capacity is a property of the table. This information "belongs with" the table entity. If table assignment is a more chaotic affair, meaning that we don't want to codify it in our app, then we might simply leave it as a text field where a user can put any description.

Deciding whether something in our domain is an entity or a property should not blind us to certain facts. Always keep in mind the problem domain is more complicated than our model. Models are projections of the real thing. They're incomplete and rigid, unlike the chaos of the physical world. Consider our restaurant tables. Realistically, tables can also be combined to create larger tables. How, if at all, do we determine the capacity of such a construction? The dining area of a restaurant is hardly ever fixed. When a client asks us to build an app, we as developers are responsible for questioning it. Our job, when modeling, is to discover likely scenarios and decide how they should be represented. And if a scenario is unlikely, do we accept our model's shortcomings (forcing users to work around the limitiations), make room for exceptions, or make it sophisticated enough to support it?

## Car dealership accounting
A customer of a car dealership can purchase a car. It's common that a customer only ever buys one car at the dealership, but it would be crazy if that dealership's software could not support/represent the same person buying multiple cars. So, here is a not-so-likely scenario that is, considering the consequences, still very much worth modeling. Before reading on, think about this: is there a relationship between the customer entity and the car entity?

When a customer buys a car, an order is created. Having a relationship between the customer and car entities seems odd. Why? For one, the relationship "customer-buys-car" itself also has properties. These properties belong somewhere on an entity. In the problem domain there already is an entity for that purpose: the order. An order is associated with a car. A customer entity has a relationship to the car entity, but _not directly_. The relationship is _through_ the order entity.

The cardinality of the relationship between a customer and an order is "one-to-many". A single customer can be associated with many orders. Reasoned in the opposite direction: an order can only be associated with a single customer. An order can be associated with multiple cars. That relationship is also "one-to-many". Or is it? Before reading on, can you think of a scenario where a car can be associated with multiple orders?

What if a customer comes back and returns the car? If it's still represented by the same car record (same ID) then a one-to-many relationship between the order and car entities makes it impossible for us to sell the same car again. We would have to remove the association with the order, or delete the order record altogether. But that's bad, because we'd be losing information. One solution (but certainly not the only one) would be to make the relationship between order and car a "many-to-many" relationship. Meaning that an order can be associated with multiple cars, and a car can be associated with multiple orders. The trade-off is that our model now supports selling a car more than once. Meaning that our code (not the database) will be responsible for checking the status of an order before associating it with a car. Unfortunately, in its current state, our model cannot guarantee that only unsold cars can be sold, leaving room for a potential bug. Ideally, we would make that bug impossible, by making the invalid state impossible to represent. Can you think of a solution that will let the relational database guarantee our data's validity?

# How-to do entity modeling
So far we've discussed scenarios that should give you _some_ idea of what to consider when modeling. But wouldn't it be amazing if there was a single, clear, list of steps to follow that will always lead to a perfect model? Yes, that would be amazing. But (you can probably tell where this is going) there is no such list. The reason: every choice is a trade-off. You get something, but lose something else. With experience, you will learn to understand the trade-off and make better decisions. The following list is an outline of steps to design a model. It won't answer all the questions, but it makes a chaotic process a little more structured. Models evolve during the life of a project. So, the list is actually an infinite loop.

1. Explore the problem domain with an expert (usually product owners are domain experts)
1. Gather requirements of what the software should be able to do.
1. Write down all the (new) domain terminology. Nouns and verbs with special meaning in the domain. That is, jargon.
1. Sort the words into entities, properties, and relationships. Add them to the (existing) model.
1. Test model against the software requirements. For each feature, ask "can the model represent this case"?
1. Attempt to think of exceptional cases that are not part of the requirements. Consult with the domain expert. Address any issues by deciding (explicitly) to ignore them, or by extending the model.
1. Consider whether any invalid states can be represented by the model. If you can, adjust the model to make these states impossible.
1. Simplify. Remove any entities or relationships if you discover that you do not (currently) need them.
1. Go to step 1.

# Exercise: Create a model
Create a model for a company restaurant. They want to manage and publish their weekly menus online. These are the client's instructions.

A day menu looks like this:
![a sample day-menu](https://cl.ly/08157c80ab93/Image%202019-06-26%20at%202.58.26%20PM.png)

- There is a rotating set of 5 weekly menus.
- A week menu is made up of 5 day menus, one for each weekday.
- We need to be able to make one-time changes to a menu that only apply to one week.
- When users visit the website, they should be able to see the current and next week's menus.
- Sometimes there are salads on the menu.
- We're considering adding desserts to the menu, so please add that functionality.
- On one of the days, the tomato soup has meatballs. So, it's not always vegetarian. Make sure that's clearly visible.

_Note: The requirements are written as a non-technical person might say them. Consider it a challenge to figure out how they translate to the model, if at all!_

1. Find one or two teammates for this exercise. Use pen and paper or a white-board, if you can find one.
1. Follow the steps in "How-to do entity modeling".
1. Use simple notation: labeled boxes and arrows. Don't forget to indicate cardinality.
1. Iterate on the model for 15 minutes, then stop. There is no perfect solution, and the requirements don't cover all possible scenarios. 

# Normalization
Normalization is the changing of a data model to reduce duplication and increase our ability to inspect it. Reducing duplication ensures there is only one answer to a question about the data. Putting all the information in its correct form in the database lets us use all the power of SQL. Here are some normalization guidelines, plus explanations of why they're a good idea:

1. **A table should not have multiple columns to represent the same information.**
   
   Why? Doing so is misusing columns to simulate a one-to-many relationship. Adding multiple instances of a relationship would mean altering the structure of the table. This needs to be stored in a separate table.

   Non-normalized (bad) modeling example:

   | id | boss | employee_1 | employee_2 | employee_3 |
   |---|---|---|---|---|
   | 1 | Wouter | Kelley | Arien | David |
   
   What if Wouter hires someone new?
1. **Entries in a column should be of the same type**
   
   If not, there would be no consistent meaning to the column name or its type. We would need extra information to determine what is in the cell.

   Non-normalized (bad) modeling example:
   
   | id | name | age |
   |---|---|---|
   | 1 | Kelley | 27 |
   | 2 | Elsie | "21 years" |
   
   How do we programmatically deal with these ages?
1. **Each table cell should contain a single value.**

   If cells contain multiple values, then we cannot use SQLs power to query the contents. Lists of things should be stored as separate rows in the database.

  Non-normalized (bad) modeling example:

  | id | boss | employees |
  |---|---|---|
  | 1 | Wouter | Kelley, Arien, David |

  Think about adding a new employee: this would be a string concatenating operation ... messy! And how would we iterate over, or filter, employees?
1. **Each record needs to be unique.**

   If not, then we cannot distinguish two rows. There would be no identity to the information. It would not be possible to _target_ any single one of those rows with a `WHERE` clause, because all their cells would be equal. Do those rows represent the same thing, or two distinct things? No way to know.

   Non-normalized (bad) modeling example:

   | id | name | age |
   |---|---|---|
   | 1 | Kelley | 27 |
   | 1 | Elsie | 21 |
   
   What?
1. **Every (non-key) column should depend on the key.**

   If a column does not depend on the identity of the row it's in, then it's not really a property of that type/entity. These columns deserve their own table.

   Non-normalized (bad) modeling example:
   
   | id | name | country_of_origin | num_states |
   |---|---|---|---|
   | 1 | Kelley | USA | 50 |
   | 2 | Elsie | USA | 50 |
   
   How does the fact that USA has 50 states relate to Kelley?
1. **Changing a non-key column, should not cause any of the other non-key columns to change.**

   Why? Because it would mean that there is a relationship (the two non-key columns are _related_, that's why they change together). The relationship should not be coded into a single table. Like in the previous rule, this embedded entity deserves its own table.

# WARNING: Denormalizing is for advanced users
If normalizing decreases duplication and increases structure, then denormalizing increases duplication and decreases structure. That might sound bad, but there _is_ value to denormalization. In a normalized model, data is spread across tables and joining them is required to traverse the relationships. For performance reasons, you might duplicate some data so fewer joins are necessary. The trade-off is that it's now possible (for example: through programmer-error), for the duplicated data to become out of sync.

Duplication in service of performance is rarely necessary. Relational databases are very efficient at joining tables. Denormalizing can always be done later. Normalization, however, can be a nightmare to do if you have lots of existing data. Denormalizing can also be a choice you make when data is distributed across several servers in a large system to achieve better resilience in the face of hardware failure.

A huge red flag that you're doing something wrong is denormalizing out of laziness. If your argument for not structuring your data is: "it might change later", "fetching relationships is complicated" or "I want to be free to store whatever I want", then it's likely that you're digging yourself into a hole.

# Quiz

<MultiChoice
  id="is_a_perfect_model_a_thing"
  question="There is always a single best way to model anything."
  options={[
    "No",
    "Yes"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="in_db_entities_are_stored_as_tables"
  question="Entities are stored in relational databases as what?"
  options={[
    "Tables",
    "Columns",
    "Rows",
    "they're not"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="in_db_relationships_are_stored_as_fks"
  question="Entity relationships are stored in relational databases as what?"
  options={[
    "Foreign key relationships",
    "Table unions",
    "Join tables",
    "they're not"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="not_normalizing_model_limits_what"
  question="If you don't normalize enough, it may limit ..."
  options={[
    "The level of detail you can query",
    "The amount of data that can be stored",
    "The performance of your queries",
    "Your salary"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="example_of_database_model_duplication"
  question="What is an example of duplication in a database model?"
  options={[
    "Two foreign key relationships that represent the same 'real' relationship",
    "Two rows where every cell except the IDs are the same",
    "Two tables with the exact same column names and types",
    "Two columns with the same name, in different tables"
  ]}
  shuffle={true}
  correctIndex={0}
/>

