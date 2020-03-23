---
title: Introducing REST
---

# Introducing REST
Most texts describing the benefits of REST, start by describing the horrors of SOAP. For our purposes, REST and SOAP are two different approaches to standardizing the communication between a client and a web service. Being aware that SOAP exists will suffice for now. Going into the details won't help you in the short term. So we will leave that as an exercise for the reader.

REST stands for REpresentational State Transfer. It's an architectural style that incorporates many of the ideas and technologies that the web was built on. We will use this style to shape our web services. The REST style is not very prescriptive. Meaning, it can be difficult to judge whether a web-service satisfies all the constraints of the REST style. Although it can be subjective and hotly debated, some things are generally accepted as _RESTful_ (meaning: in accordance with REST). In this guide we will focus on these:

1. Clean URLs
1. Operations as HTTP methods
1. Appropriate use of HTTP status codes

## Clean URLs
The REST definition talks about "web resources". The actual data of such a resource can be a file on disk, or a record in a database. However, from the point of view of the client, all that matters is its location. More precisely, its Uniform Resource Locator (**URL**). In the REST style, a URL has a meaningful structure:

```bash
# URL corresponding to the user record with the ID 943
https://example.com/users/943

# URL corresponding to the collection of user 943's documents
https://example.com/users/943/documents

# URL corresponding to one of the user 943's documents, namely, the one with ID 25621
https://example.com/users/943/documents/25621

# URL corresponding to the collection of all users records
https://example.com/users
```

Some URLs indicate **collections** (like `/users`), while others correspond to **single resources** (like `/users/943`). This is an important distinction! The meaning of requests varies slightly between the two. Which brings us to our next topic...

## Operations as HTTP methods
If all a client needs is a resource's location (**URL**), how does it know what it can do _with_ (or _to_) that resource. The REST architectural style says that you should interact with resources through the available HTTP methods: **GET, POST, PUT, DELETE, PATCH** etc. These methods already have associated semantics in the HTTP standard. The meaning of each HTTP method depends on whether the request is made to a _collection_ or _single_ resource URL. Commonly accepted handling of these methods include:

- `POST`: Create - Creates a new resource inside a collection resource. This method is usually not used for single resources.
- `GET`: Read - Retrieves a resource's representation, or all representations inside a collection
- `PUT`: Update - Updates a resource with the representation provided by the client
- `PATCH`: Update - A special type of update. It merges the existing resource with the representation provided by the client
- `DELETE`: Delete - Deletes the identified resource, or all resources inside a collection

These operations, together, are often called a _CRUD_ interface. An initialism for **C**reate **R**ead **U**pdate **D**elete.

## Appropriate use of HTTP status codes
HTTP requests can succeed or fail for various reasons. The REST architectural style requires us to use the HTTP status codes to indicate the result of requests. These codes are part of a uniform interface. Regardless of which web service you're calling, a `404`-status always means the same thing: `Not Found`. This makes working with new web services easier, because they behave (mostly) the same.

Here are the ranges of responses and their general meaning:

- `200` - `299`: Successful responses
- `300` - `399`: Redirection messages
- `400` - `499`: Client error responses
- `500` - `599`: Server error responses

## Some things do _not_ fit REST
The three aspects of RESTful web services, as they are commonly used, are well-suited for CRUD operations. However, some operations do not fit that model very well.

Let's say you want a web service that sends an email. If we try to make it conform to the constraints of URL structure and HTTP status codes and methods, we might come up with something like this:

```bash
# sends an email
POST - /mail
# retrieves the contents of an email with ID 123
GET - /mail/123
```

But this suggests that we're interested in creating and retrieving the contents of an email. Perhaps that's the case, and surely it can be useful, but the _purpose_ of the API is to _send_ the email. Persisting the message comes (incidentally) with us wanting to treat it as a resource, rather than what it really is: a procedure. Forcing such procedures/actions/jobs into a REST resource can produce some unintuitive interactions and false expectations. It is better to implement them as remote procedure calls (RPCs).

```bash
# The path clearly suggests that this action has the side-effect of sending an email.
POST - /sendEmail
```

The web service police will not arrest you if you choose to build an API using a mix of REST and RPC end-points. Although, REST-purists may want to. Other examples of actions that do not fit the CRUD resource model very well:

1. Downloading a PDF version of a text document.

   The PDF does not qualify as a resource, because it can only be _read_. Changes to the original document affect the PDF. It is not its own entity, but a transformation/projection. The PDF generation might be expensive. A path `/generatePDF?docId=5` communicates that point more clearly than `/document/5/pdf`. The latter could be interpreted as meaning that the PDF already exists, and you're simply retrieving it.
1. Transferring money between two accounts.

   This is an operation across (at least) _two_ records. Since each of them are exposed as resources, how do you map your transfer so that it's a `PUT` or `POST` on a single resource? Should the transfer itself be its own resource? With enough creativity you can think of something that makes REST-sense. But it raises the question: Why try so hard to retro-fit a procedure into a resource? What are we gaining? Are they actually more intuitive, or are we trying to enforce a subjective preference?
1. Cleaning up old data.

   There are many such actions which we call "batch-jobs". They apply some logic and operate on many records at once. Often you want to have the ability to start and stop such jobs, because they take a long time to complete. Such actions are difficult to map to a resource.

Here are some signs you're using the wrong tool for the job.

1. If you find yourself defining REST resources that can only be created, but cannot sensibly be updated or deleted.
1. If they have some side-effect other than persisting of the data itself.
1. If reading a resource seems to be incidental, and not essential. 

If any of the above is true, then consider creating an RPC end-point instead. Put an action verb in the URL (like `/startCleanup`) and map it to the `POST` method. According to the HTTP standard, `POST` is the default method to handle data processing with side-effects.

## Quiz

<MultiChoice
  id="1563886390590"
  question="What do you know about SOAP?"
  options={[
      "Most people hate it",
      "It's better than REST"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="1563886390591"
  question="The original description of REST can best be described as ...?"
  options={[
      "An architectural style that builds on top of HTTP and URLs",
      "A standard for implementing web-services"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="1563886390592"
  question="What is a CRUD interface?"
  options={[
      "an API for creating, reading, updating and deleting one particular type of resource.",
      "a web-service that supports `GET`, `POST`, `PUT` and `DELETE` HTTP methods."
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="1563886390593"
  question="What should a `PUT` request to a single-resource URL `/documents/123` do?"
  options={[
      "update the resource with the representation provided by the client",
      "create a new resource with the ID `123`",
      "update the resource's ID to `123`",
      "create a new sub-resource inside the document with the provided representation"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="1563886390594"
  question="What should a `DELETE` request to a collection-resource URL `/documents/` do?"
  options={[
      "delete all documents",
      "delete the document matching the representation provided by the client"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="1563886390595"
  question="What should a REST client do when it receives a `400` error status?"
  options={[
      "adjust/fix the request and try again",
      "log the error, since you cannot recover from it"
  ]}
  shuffle={true}
  correctIndex={0}
/>


<MultiChoice
  id="1563886390596"
  question="How is an RPC endpoint different from a REST endpoint"
  options={[
      "Their behavior is not standardized, so you have to read the documentation to find out exactly what it does.",
      "RPC endpoints can be slow, so you have to be careful how frequently you call them.",
      "REST endpoints always come in sets: Create, Read, Update, and Delete",
      "They're actually very similar"
  ]}
  shuffle={true}
  correctIndex={0}
/>
