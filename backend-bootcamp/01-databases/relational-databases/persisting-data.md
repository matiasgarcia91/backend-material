---
title: "Persisting data"
---

# Persisting data


## Where and when does your data live?

Applications have to manage *data*. They present it, and update it when necessary. For example, a Tinder-like app will contain lists of possibly interesting people, and will store likes and swipes and who knows what else. Apps like YouTube, Reddit, etc. let people discuss through comments. A calendar app manages events and appointments. And your public transportation planner-app presents you with the most efficient way to go from A to B.

But where does this data "live"? If it only lives on the client (front end), then data cannot be shared. If it only lives on the server (back end) in memory (variables, objects, and arrays), data would be lost when we restart the app. In order to avoid this problem, we need to **persist** the data. This means storing it somewhere in such a way that it can *outlive* the duration of time that your app is open.


## What databases have to offer

Persisting data does not need to be complicated. The simplest solution would be to just save the data to files. And of course, you'd typically want to save it on a server somewhere, instead of the place where your application front-end "lives" (i.e. a mobile phone or the browser). We want to access the data from other devices, and the browser's storage capabilities are limited and untrustworthy. This means that you'd have to write some code that "lives" in that location (e.g. a server), which writes those files when you ask it to, and reads back the data when you ask it to.

And in fact, databases do precisely that. A database is essentially just a program that saves your data to files when you ask it to, and retrieves data from those files when you need it. It typically lives on the server-side of your application.

But databases do more than just that. Data is often the backbone of a good application, and managing it can become quite hard for reasons like the following:

- It must be stored, searched and retrieved *efficiently*, even though you might accumulate large/unknown amounts of data over time.
- It must be stored in a *future-proof way*, so that you can *query* your data in different ways in the future than you're doing right now. For example, if you start out saving each person's "file" in a separate file, but in the future, you want to be able to ask questions like *"What is the total amount of different types of pets owned by all graduated Codaisseur students who still live in the Netherlands?*, you can get into trouble.
- It must be stored in a *fault-tolerant* way, because it's a critical part of your business. Computers and their hardware are always prone to errors, and of course there's always the simple power outage, so you'll want to take measures to ensure that the data is not corrupted (or lost).

Because storing and managing data turns out to be a complicated task, *databases* exist to solve exactly that problem. Databases store your data in an efficient, future-proof, easily searchable, fault-tolerant way.


## Using databases

Moreover, because databases "abstract away" the physical storage of your data ("they just deal with it, you don't need to know how"), they provide an *interface* through which you talk to them. Via this interface, you can then *manage*, *query*, and *update* the data.

The next chapter will introduce the most commonly used type of databases, called "relational databases".

