---
title: Docker
tags:
  - Docker
---

# Docker
Docker is a tool to create, run, and share containers. It has brought containers to the masses. Thanks to the Docker project, many aspects of containers have been standardized.

## 🎯 Learning Goals

You will learn...

1. What Docker is.
1. What images are and how they relate to containers.
1. How to manage containers using the `docker` command.

## 📚 Resources
Not required reading:

1. [Docker Quickstart](https://docs.docker.com/get-started/)

# Installation

Whatever your system is running, it can run Docker! Pick the correct link for your system.

Follow the install instructions for:
  * [Docker for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/).
  * [Docker for Mac](https://docs.docker.com/docker-for-mac/install/).
  * [Docker on Windows](https://docs.docker.com/docker-for-windows/install/).

> Linux users! We recommend you also perform the "[Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/)". Otherwise, you will have to use `sudo` to run most docker commands.


Complete the installation instructions. To verify that docker works, let's run a simple Docker command:

```shell
docker run hello-world
```

If all is well, you will see something like this after a while:

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker Hub account:
 https://hub.docker.com

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```

This container's _main process_ wrote this message to its output. The `docker` command line tool forwarded that output to your terminal. So, even though the container ran in isolation, the output was still visible to you. Let's look at that more in depth...

# Images and containers
In Docker terminology, images and containers are two related, but different concepts. Understanding them is central to understanding Docker.

An image includes the data and configuration needed to create a container. Containers are _instances_ of images. You create a container by "running" an image:

```console
$ docker run my-image
```

Images can be specified using names or IDs. In the above example it is assumed that there is an image named `my-image`. Once created, a container also gets a name and ID. Names and IDs must be unique. When running an image (meaning: creating a container) the name can be specified on the command-line. If not, it will be automatically generated by Docker. Here is how you specify a name (if you want to try this command for yourself, use a real/existing image name like `hello-world`):

```console
$ docker run --name=pluto my-image
```

If you were to run the same command a second time, without removing the previously created container, you would get an error like this:

```
Conflict. The container name "/pluto" is already in use by container "...". You have to remove (or rename) that container to be able to reuse that name.
```

This is true _even if the container has stopped running_! Stopping and removing a container are two different actions. A container is an environment running one or more processes. Once the main process terminates, the container is no longer active. However, a container's data (like its virtual file-system) still remains, until we remove it. Removing a container can be done with the `docker rm` command.

## Exercise: Creating and removing containers
1. Run the `hello-world` image and make sure that the container is named **`test1`**.
1. Run the command a second time. Read the error message carefully.
1. To remove the stopped container, run:
   ```console
   $ docker rm test1
   ```
1. Read the output of the command carefully and confirm that the container was removed.
1. The `rm` sub-command also accepts a container ID. The error message in the second step contains such an ID. Repeat the steps in this exercise. This time, use the container ID when removing the container.

# Background processes
When you execute the command `docker run hello-world` you can see the output of the container's main process. The reason for this is that that process is attached to your terminal. The `docker` command forwards the output from the container to your terminal. It also works in the other direction, from your terminal to the container. So, any signals like <kbd>Ctrl</kbd>+<kbd>C</kbd> will cause the container's main process to terminate.

Sometimes this is what you want, but, very often, it's not. To run a container in the background (meaning: _not_ attached to a terminal), you can add the `-d` option to the `run` command.

> Note: All the command-line options of the `run` sub-command must appear _before_ the image name.

Here is an example, using a fictional image named `my-image`:

```console
$ docker run -d my-image
```

Containers running in the background can be inspected. The following commands will show you the status of the containers on your machine.

```console
# This command lists the running containers.
$ docker ps

# This command lists all containers, both running and stopped.
$ docker ps -a
```

To terminate a container running in the background:

```console
$ docker stop
```


## Exercise: Creating containers in the background
1. Run the following command. Notice the `-d` option:

   ```console
   $ docker run -d hello-world
   ```
   
   The text printed to the terminal is the container's ID.
1. Since the container has exited, run `docker ps -a` to see it.
1. Now do it again. Run a web server called Nginx by starting a container **in the background**. The image name is `nginx`.
1. The server should be running in the background. Run `docker ps`, without the `-a` option, and the container should be listed. It will also include information about how long it's been running. Notice the output contains a column `PORTS` and for our container it shows `80/tcp`.
1. Stop the container using `docker stop` and the container's ID or name.
1. Run `docker ps` again and confirm that the container is _not_ listed. Meaning, it terminated.
1. Remove the container with the `docker rm` command. You will be using the same ID/name as with the `stop` command.
1. Run `docker ps -a` and confirm that the the Nginx container is gone.

# Mapping network ports
As you may recall, server processes on our computer _listen_ for requests. They listen on a particular port. For HTTP the default port is number `80`. If we want to run multiple web servers on the same computer, they will have to be configured to use different ports.

Containers get a virtual network device. Since containers are isolated from one another, two web servers in different containers can both listen on port 80 as in this example output of a `docker ps` command:

```console
$ docker ps
CONTAINER ID    IMAGE     COMMAND                  CREATED           STATUS            PORTS    NAMES
4d1cfd868005    nginx     "nginx -g 'daemon of…"   4 seconds ago     Up 2 seconds      80/tcp   stoic_ganguly
314c3259abd8    nginx     "nginx -g 'daemon of…"   15 minutes ago    Up 15 minutes     80/tcp   hopeful_volhard
```

The `nginx` container exports port `80`. By exporting a port, the author of the container image specifies this port as being potentially usable by the outside world. But, unless we tell Docker how to map that port to our host system, those ports cannot be reached. To map a port, means to connect a port on our host, to a port inside a container. Take a look at the following example:

```console
$ docker run -d -p 3000:80 nginx
```

The `-p` option is used for port mapping. The first number represents the port on our host, and the second is the container-side port. Thanks to port mapping, you can easily run the same image and expose ports by mapping them to different unique numbers.

## Exercise: Port mapping
1. Run the `nginx` image in the background, and map the container port `80` to the host port `8888`.
1. In your browser, visit `http://localhost:8888` and confirm that you are greeted by an Nginx message.
1. Run a second `nginx` instance in the same way, but map the HTTP port to `8889`.
1. Visit the second Nginx instance in the browser by changing the port number in the address bar.
1. Stop and remove the Nginx containers.

# Environment variables
Many container images are configurable through environment variables. These can be specified on the command line when creating a container:

```shell
$ docker run -e SOME_DATA="this is some data" my-image
```

## Exercise: Specifying environment variables

1. In your terminal (which is running a shell like Bash or Zsh), set an environment variable called `MESSAGE` like this:
   ```shell
   $ MESSAGE='Apples are delicious'
   ```
1. Print the value of the environment variable using `echo`. The command will refer to the variable name, but we need to prefix the name with the `$`-sign, like so:
   ```shell
   $ echo The message is: $MESSAGE
   ```
1. Now, run a shell, but inside a container. The following command will create a container from the `ubuntu` image, which, by default, starts a Bash shell session. Notice that your command prompt will change.
   ```shell
   $ docker run -it ubuntu
   ```

   The `-it` option allows us to interact with the shell inside the container.
   
   The command prompt will look something like this:
   ```shell
   root@2a2ea887f152:/#
   ```
1. At the command prompt, type a command that prints the value of the `MESSAGE` variable to the terminal, like you did before. Confirm that the message is empty.
1. Set the `MESSAGE` variable, but with a different value:
   ```shell
   $ MESSAGE='Worms like apples'
   ```

   Print the message using `echo`, and confirm that the message is properly set.
1. Exit the shell (which also terminates the container), by typing `exit`. Confirm that you are back at your regular shell/command prompt.
1. Print the value of `MESSAGE` to confirm that it still has its old value. The changes inside the container did not affect the variables in the shell session that started it.
1. Now start another container from the `ubuntu` image with an interactive shell. But, this time, define an environment variable in the command using the `-e` option. Give it any value.
1. At the container's command prompt, use `echo` to print the value of your environment variable from the previous step. Confirm that the environment variable exists and its value is properly printed.
1. Exit the shell to terminate the container and go back to your original shell session.

# Conclusion
Here are some key take-aways:

1. Containers pre-date Docker, but Docker simplifies the use of containers.
1. The images can be `run` to create instances, which we call containers.
1. The file-system of a container is "fake"/virtual.
1. Container names are optional, but they have to be unique. 
1. Containers can be stopped (`docker stop`), started (`docker start`), and removed (`docker rm`). Until they are removed, their name remains "claimed".
1. You can show a list of running containers with `docker ps`, and a list of ALL containers with `docker ps -a`.

# Quiz

<MultiChoice
  id="what_does_docker_run_do"
  question="What does `docker run` do?"
  options={[
    "create a container from the specified image",
    "create an image from a running container",
    "start a stopped container",
    "that command does not exist"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="what_are_container_images"
  question="What are container images?"
  options={[
    "blue-prints for containers",
    "isolated environments for processes",
    "stopped containers",
    "the same thing as containers"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="what_do_you_have_to_do_in_order_to_re-use_a_container_name"
  question="What do you have to do in order to re-use a container name?"
  options={[
    "Both stop and remove the container with that name",
    "Only stop the container with that name",
    "You cannot ever re-use a container name",
    "Containers don't have names, only IDs"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="what_is_a_container_is_running_in_the_background"
  question="What does it mean when a container is running in the background?"
  options={[
    "You don't see its output in the terminal",
    "It doesn't consume as many resources",
    "It runs at a lower priority as foreground containers",
    "It cannot be stopped from the terminal"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="why_is_port_mapping_needed"
  question="Port mapping/publishing is necessary to do what?"
  options={[
    "make container ports available on the host",
    "allow containers to initialize connections with the outside world",
    "to let containers listen for requests from other containers",
    "all containers must publish ports"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="which_docker_dash_p_port_is_the_second_one"
  question="When mapping a port with `-p 1234:5678`, what is the second number `5678`?"
  options={[
    "it's the port that a server inside the container listens on",
    "it's the port on the host that we want to map to"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="what_are_docker_command_env_vars_used_for"
  question="What are environment variables on the `docker run` command commonly used for?"
  options={[
    "passing configuration to the container",
    "reading information from the container"
  ]}
  shuffle={true}
  correctIndex={0}
/>

<MultiChoice
  id="what_does_docker_ps_dash_a_do"
  question="What's the difference between running `docker ps` with and without the `-a` option?"
  options={[
    "if you include the option, you also see all the stopped containers",
    "if you include the option, you also see previously deleted containers",
    "if you include the option, you do not see the running containers",
    "there is no such option"
  ]}
  shuffle={true}
  correctIndex={0}
/>
