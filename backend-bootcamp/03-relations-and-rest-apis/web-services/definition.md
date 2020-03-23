---
title: What is a web-service?
---

# What is a web-service?

The term API gets thrown around a lot. Often, what people really mean to say is "web-service". So, let's clarify.

## Application Programming Interface (API)

An API is the set of procedures (read: functions) that a software component can use to communicate with another component. It applies to the scenario of code calling _other_ code.

Contrast this with a UI (user-interface), where a human is using a piece of software. Applications collaborate through **code**, while users interact with software through the monitor, keyboard, and mouse.

When you choose to use a library in your program, you might install it (using a package manager) and read the documentation on how to use it. The documentation will cover the library's API. That is, the parts of its code that _your code_ is supposed to call. APIs are a more generic concept than...

## Web services

A web service is an API available on the web. In other words, functions you can call by making HTTP requests. So, all web services are APIs, but not all APIs are web services.

A web server response containing HTML is (generally speaking) meant to be rendered for, and used by, humans using a browser. When a web server responds to HTTP requests in a standardized data format like XML or JSON, then we're dealing with a web _service_ intended to be used by another program. Yes, a human developer can use JSON, but developers are not normal people. Or at least, they're not the end-user.

## APIs: self-driving car analogy

Say you were asked to develop a self-driving car. You don't want to invent a new car from scratch (at least, not yet). Meaning, you're likely going to build on top of an existing car. The car you choose will have a user interface. Designed for humans. A steering wheel with ergonomic grips for human hands. Gas and break pedals at the right distance from the seat for human legs. To control the car, you'd have to build something mechanical that would operate the human interface. This is a difficult problem to solve on its own. And it's also hard to make it work for all car models. Each having their own dimensions and layout.

It would be faster and easier to focus exclusively on the logic, and connect to a car programming interface. Your program might run on a small computer and plug into the car via a USB cable. That would be an interface designed for applications: an API.

Imagine that the car interface is standardized. You could buy a car and have the option of choosing different brands of car "brains". Competition between manufacturers would increase. The barrier of entry for new companies would be lowered. In the end, the customers benefit. This is the effect web services have had. Thanks to web APIs like Google Maps, many websites can now offer functionality that they wouldn't otherwise have had the resources to develop.

## Quiz

<MultiChoice
id="1563886390582"
question="Every API is a Web Service, but not all web services are APIs."
options={[
"False",
"True"
]}
shuffle={true}
correctIndex={0}
/>

<MultiChoice
id="1563886390584"
question="What makes a service a _web_ service?"
options={[
"That you talk to it through HTTP",
"That it's used from inside web-pages"
]}
shuffle={true}
correctIndex={0}
/>
