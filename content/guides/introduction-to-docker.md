+++
title = "Introduction to Docker"
description = "This guide gives an introduction on how to use Docker locally as well as with Giant Swarm."
date = "2015-05-19"
type = "page"
weight = 20
categories = ["basic"]
+++

# Introduction to Docker

## What is Docker and why should I care?

> "Self-sufficient linux containers." - Docker Inc.

Docker is the most popular application container format. It lets you package your own stack in lightweight, portable, and self-sufficient manner. Some call it therefor "lightweight VM". In contrast to traditional VM's it's an order of magnitude faster, lighter, and more fun. ;-)

Docker makes a great tool for developers since it allows you to package different parts of your applications in their own containers. These containers can run on different environments like your and your colleagues’ notebook as well as different server environments like Giant Swarm.

Another great aspect of Docker is that there are lots of predefined containers available. From different languages from JavaScript to Golang over different web stacks demo Spring to Rails down to databases like Redis or MySQL. In addition defining your own Docker container is very easy.

Docker together with Giant Swarm will make your life a lot easier. You will use the `docker` client to build and push containers and `swarm` to deploy, start, and manage your applications.

## Getting started with Docker

In this section we will give you a hands-on introduction to working with Docker. If you already know how to work with Docker you can skip this. If not you should at least have a look and get used to working with containers and images, as you will need these for working with Giant Swarm.

Before we start let’s make sure that your have [Docker installed on your development environment](https://docs.docker.com/installation/). On Linux this should be pretty straight forward. As Docker uses Linux containers the installation is slightly more complex on a Mac. The easiest and for beginners recommended version is installing Boot2Docker. It has an easy installer and installs Virtualbox and a Boot2Docker virtual machine to host your Docker daemon and containers. There’s easy [installation instructions in the Docker Documentation](https://docs.docker.com/installation/mac/), however, we also created our own very detailed [guide to installing Boot2Docker](https://github.com/kordless/boot2docker-ing), which even includes a [video tutorial](https://vimeo.com/120645766).

After that you should have the docker command available. You can run a first Hello World like follows (keep in mind that if you’re running the docker daemon directly on your system, i.e. on Linux, then you need to put ‘sudo’ in front of every docker command):

```nohighlight
$ docker run hello-world
```

This will pull the Docker hello-world image (and all underlying images) and run them on your Docker daemon. So, let’s get started and familiarize ourselves with running containers.

## Running Containers

We start with a deeper look into what we can do with `docker run`

```nohighlight
$ docker run debian:wheezy /bin/echo 'Hello world'
```

What this does, is download and run the Debian container in its “wheezy” version (we call these tags, but more on that later) and run `/bin/echo 'Hello world'` inside that container. What happens after that, is that the container stops again. Docker runs the container only as long as the command you specify is active, so if you want your container to stay, give it a command that stays active. A lot of containers come pre-built like this, e.g. the standard Redis container:

```nohighlight
$ docker run redis
```

Note that this image uses debian:wheezy as its base image, so the download is a lot faster this time, as we only need the additional layers on top of that now (more on that later). However, now you’re stuck inside the container, which wouldn’t be what you want, if you usually start a Redis. Thus, we need a way to start a container in the background:

```nohighlight
$ docker run -d redis
```

Now we have a daemonized Redis and could connect to it from outside or from other containers. We do this by connecting to the IP of our docker daemon, which in boot2docker we get with:

```nohighlight
$ boot2docker ip
```

However, we have not specified a port, so let’s do this again, this time with a port. Let’s stop our container. For this we need the container ID, so first:

```nohighlight
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
113f2a688eb2        redis:latest        "/entrypoint.sh redi   30 minutes ago      Up 5 seconds        6379/tcp            goofy_wozniak
```

Now that we have the Container ID: `113f2a688eb2 ` (note this will be different every time you run an image), we can stop the container:

```nohighlight
$ docker stop 113f2a688eb2
```

To make this easier we can assign a name to a container, let’s combine that with assigning ports:

```nohighlight
$ docker run -d -p 9736:6379 --name redis_container redis
```

We just started a Redis container in the background, which is called redis_container. It exposes the host port 9736, which it connects to port 6379 (the standard Redis port) inside the container. So now, let's work with it.

## Working with Containers

Now that we know how to run containers, let’s see how we can work with them.

To see which containers are currently running we can use the `docker ps` command. AS this does not show any stopped containers we can use `docker ps -a` to show all containers that we have.

To look at our application’s logs we can use `docker logs` like following:

```nohighlight
$ docker logs -f redis_container
```

The `-f` flag makes the command work like `tail -f` and watch the standard output of the container.

If we want to go deeper into the container we can also let Docker show us the processes running inside a container:

```nohighlight
$ docker top redis_container
```

Going even deeper we can let Docker give us an overview of configuration and status information for a given container:

```nohighlight
$ docker inspect redis_container
```

Stopping and restarting a container is simple, too:

```nohighlight
$ docker stop redis_container
$ docker start redis_container
```

In case we don’t want the application anymore we can also remove it completely. Keep in mind that for removing a container you need to stop it first:

```nohighlight
$ docker stop redis_container
$ docker rm redis_container
```

Now `docker ps -a` shouldn’t show our container anymore.

## Working with Images

Images are the basis of containers in Docker and as you will see also in Giant Swarm. We have worked with images already in the previous sections of this guide, but let’s go into a bit more detail here. We’ll start with simply listing all images on our host:

```nohighlight
$ docker images
```

You should see the image we used in the previous sections and some crucial information about them, incl. their repository, tags, the image ID and the size.

If you want to just download a new image without (or usually in preparation for) running it. You can use ‘docker pull’:

```nohighlight
$ docker pull hello-world
```

You can see that docker downloads all layers that this image consists of. In case you have a different image on your host that already uses some of these layers, docker just keeps that and skips downloading that part of the image. This makes the process of running new instances or variations of your stack extremely fast.

If you want to find public images the Docker Hub has a nice searchable interface with many standard images, incl. some basic images that are officially maintained supported from Docker. If you want to search for images on the command line you can use the ‘docker search’ command:

```nohighlight
$ docker search redis
```

## Creating your Own Images

There’s two ways to create your own images:

1. You change a container that is based on an existing image and commit it to a new image.
2. You build a new image from a Dockerfile

Note, we recommend always trying to build an image from a Dockerfile, as this allows for better reproduction of and iteration on your image.

However, let’s see what we have to do to change an existing container. For changing a container you have to first run it:

```nohighlight
$ docker run -t -i --name changing_container ubuntu:14.04 /bin/bash
```

Now when you’re inside the container, you can change it to your liking, e.g.:

```nohighlight
$ apt-get update
$ apt-get install default-jdk
```

Once you’re done, you need to exit your container using the `exit` command.

Now that we have a new container, we can “save” it by committing it to a new image. This can either be a new version of an existing image, or a completely new image.

```nohighlight
$ docker commit -m “Added OpenJDK 7” -a “Mr. Smith” changing_container yourusername/java:7
```
Here we used the `-m` flag to add a commit message and the `-a` flag to specify an author. Further, we specified a target `yourusername/java:7`, which consists of a user `yourusername`, an image name `java` and a tag `7`.

This image will now be available to us locally, so we can run it like any other image:

```nohighlight
$ docker run -t -i yourusername/java:7 /bin/bash
```

Like mentioned above, the better way to create an image is to build it from a Dockerfile. For this we have create a Dockerfile in our projects directory and open it with the editor of our choice. A Dockerfile that recreates the image we created manually above would look like following:

```
FROM ubuntu:14.04
MAINTAINER Mr Smith <mr@smith.com>
RUN apt-get update && apt-get install -y default-jdk
ENTRYPOINT /bin/bash
```

Dockerfiles always consist of `INSTRUCTIONS` and `statements`. They always start with defining a base image using `FROM`. Here we chose the official Ubuntu 14.04 image. We can optionally add a `MAINTAINER` to specify who’s maintaining this image. After defining the base image we execute `RUN` commands, which usually install and/or setup packages. In the above Dockerfile we additionally defined an `ENTRYPOINT`, which specifies a command that gets run once the image has started. This way we won’t have to include this in the ‘docker run’ command. Note, that the container only runs as long as this command is running, so if this command is only short-lived the container will stop automatically.

Now let’s build our image:

```nohighlight
$ docker build -t yourusername/java:7 .
```

This will result in a similar image as the above, only that we don’t need the `/bin/bash` now anymore to run it:

```nohighlight
$ docker run -t -i --name our_java yourusername/java:7
```

Again use the `exit` command to exit the container. Additionally, you can add tags to an image after you have commited or built it.

```nohighlight
$ docker tag yourusername/java:7 yourusername/java:seven
```

Looking at our images we can see that there’s two tags representing the same image now:

```nohighlight
$ docker images
REPOSITORY                                              TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
yourusername/java                                            7                   0e5da60a9318        16 minutes ago      572.1 MB
yourusername/java                                            seven               0e5da60a9318        16 minutes ago      572.1 MB
```

The `:7` and `:seven` tags both point to the same image and will stay like that even with further updates of this image.

Finally, we can push an image to the Docker Hub, so we can share it with others or use it on other hosts (or in Giant Swarm).

```nohighlight
$ docker push yourusername/java
```

This will push the whole repository of `yourusername/java`, incl. all tags and changes to the Docker Hub. If you want to keep your images private, you can use our [private registry](/reference/registry/). This also creates a new `:latest` tag, which always points to the last image under the same name, even if the last one has a lower “version number”, e.g. `:6` (, which can happen e.g. when you fix an old version after already having a new one).

If you want to remove an image from your host, you can use the `docker rmi` command.

```nohighlight
$ docker rmi yourusername/java
```

Note that this does only delete the local images on your host, not the images that you have already pushed to a Registry.

You should now be equipped to use Docker enough, to get started and running with Giant Swarm in most use-cases. If you want to go a bit deeper, the Docker Documentation is a good place to start.

When using Giant Swarm you usually need to use the Docker client mainly for building and pushing images to either the public Docker Hub (as described above) or our private registry. For the latter you can consult our [registry reference page](/reference/registry/).
