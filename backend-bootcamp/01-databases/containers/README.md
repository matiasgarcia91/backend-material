---
title: Containers
tags:
  - Docker
new_terminology:
  - container
  - virtualization
  - guest
  - host

learning_goals:
  - id: know_what_containers_are
    description: |
      You know what containers are, and the benefits of using them.

  - id: containers_v_virtualization
    description: |
      You know how containers relate to virtualization.

  - id: containers_isolation
    description: You know that, by default, containers are 'sealed off' from the world, unless you open up communication channels.
---

# Containers

Containers are a powerful abstraction. They have changed the landscape of software development _and_ deployment.

<LearningGoals />

## 📚 Extra Resources

These are _not_ required reading:

1. [Containers at Google](https://cloud.google.com/containers/)
1. [Top 5 Benefits of Containerization](https://tsa.com/top-5-benefits-of-containerization/)

# What are containers?

Processes can run inside containers. So, a container can be seen as a closed environment for processes. It (partially) isolates them from the (operating) system they're running on. A "contained" process may have restricted access to the file-system and hardware. It's like a computer inside your computer. This concept is also known as virtualization. The file-system inside a container might be virtual (meaning: not "real").

The container is often called the _guest_, while the underlying Operating System is called the _host_. It's easy to remember this if you think of it as a dinner party. The _host_ receives _guests_ at their house. It's the host's home, and the guests are merely visiting.

# Why would you want that?

Security: To protect the system from potentially dangerous programs. After a container shuts down and is cleaned up, it's like it never existed. The consequences of running untrusted applications can be minimized.

Predictability: A container can be configured to have the same initial state each time. Each time it runs, its "world" will look exactly the same. This leads to predictable results when running applications.

Portability: Container configuration includes everything you need in order to run an application. The format has been standardized in recent years. It is easier than ever to package and distribute containers. All applications that are distributed in this way can be downloaded, installed, and run in pretty much the same way.

# What good is an isolated program?

If a program is completely "sealed off", it wouldn't be of much use. So, of course there are ways to let containers "talk to the outside world". Specific parts of the file-system can be made available to containers. Containers can also start server processes like a database or a web-server, which can then be allowed to receive incoming connections from the host's network. Connections that are made to the host system can be forwarded to the container. The point is that we have much more control over the environment that programs run in.

In the next section we are going to learn about Docker, which is a really good tool to create, run and manage containers. A simple way to look at containers would be like this diagram, separate boxes managed by Docker on top of our OS.

![Docker Containers Simple](https://p98.f4.n0.cdn.getcloudapp.com/items/DOuvB1n6/dockerSimple.png?v=9b74f6dce225653ed6cd8ab5f561ab31)

# Quiz

<MultiChoice
id="virtualization_guest_vs_host"
question="In virtualization, what do the terms \"guest\" and \"host\" refer to?"
options={[
"The guest is the virtual machine/OS, and the host is the real machine/OS",
"The host is the virtual machine, and the guest is the real machine",
"The host is the Linux kernel, and the guest is Docker",
"Two words that refer to the same thing, namely the container"
]}
shuffle={true}
correctIndex={0}
/>

<MultiChoice
id="benefits_of_container_isolation"
question="What is a benefit of running a program in isolation?"
options={[
"We can avoid potential conflicts with other programs",
"Isolated programs run faster",
"Isolated programs are light-weight and consume less memory",
"There aren't any"
]}
shuffle={true}
correctIndex={0}
/>
