+++
title = "Your first application — in Java"
description = "Your first Java application on Giant Swarm, using your own Docker container and connecting multiple components."
date = "2015-05-19"
type = "page"
weight = 55
categories = ["basic"]
+++

# Your first application — in Java

This tutorial guides you through the creation of an application using two interlinked components and a custom Docker image. The core is a Java/Spark web application. Additionally, a Redis server is used as a temporary data store and we connect to an external API.

## Prerequisites

* You should have done the [Quick Start](/) or the [Annotated Hello World Example](/guides/annotated-helloworld/) and everything should have worked fine.

* We assume that you have a basic understanding of Docker and you have Docker installed. If you don't have that, yet, check out our [Introduction to Docker](/guides/introduction-to-docker/) and come back later.

* If you want to have an easier experience testing locally, you should also have [Docker Compose](https://docs.docker.com/compose/) installed.


## For the impatient

The sources code for this tutorial can be found [on GitHub](https://github.com/giantswarm/giantswarm-firstapp-java). To facilitate following this guide, we recommend you to clone the repository using this command:

```nohighlight
$ git clone https://github.com/giantswarm/giantswarm-firstapp-java.git
$ cd giantswarm-firstapp-java
```

If you're not the type who likes to read a lot, we have a [Makefile](https://github.com/giantswarm/giantswarm-firstapp-java/blob/master/Makefile) in the repository. This file helps you get everything described below running using these commands:

```nohighlight
$ swarm login <yourusername>
$ make docker-build
$ make docker-run-redis
$ make docker-run-application
$ make docker-push
$ make swarm-up
```

Everybody else, follow the path to wisdom and read on.

## Testing Docker and getting some base images

We have a Docker task ahead of us that could be a little time-consuming. The good thing is that we can make things a lot faster with some preparation. As a side effect, you can make sure that `docker` is working as expected on your system.

We need to pull two images from the public Docker library, namely `redis` and `java:8`. Together they can take a few hundred MB of data transfer. Start the prefetching using this command:

```nohighlight
$ docker pull redis && docker pull java:8
```

__For Linux users__: You probably have to call the `docker` binary with root privileges, so please use `sudo docker` whenever the docker command is required here. For example, initiate the prefetching like this:

```nohighlight
$ sudo docker pull redis && sudo docker pull java:8
```

We won't repeat the `sudo` note for the sake of readability in the rest of this tutorial. Docker warns you if the privileges aren't okay, so you'll be reminded anyway.

While your terminal and network connection are kept busy with loading Docker images, let's have a look at what exactly we are going to build.

## Overview of our application

This diagram depicts how our application components will be set up.

![Application schema diagram](/img/your-first-application-schema-java.png)

We have one component which we call `java` as the core piece. It will provide a Spark HTTP server. When accessed by a user, it should display the current weather at our home town, Cologne/Germany.

We get the weather data from the [openweathermap.org](http://openweathermap.org/) API. Since we want to be good citizens and not call that API more often than necessary, we cache the API responses locally for a while. For this we use a Redis cache, which is our second component, called `redis` in the diagram above.

## The Java/Spark server component

Our application server consists of only one little file: A Java file called [App.java](https://github.com/giantswarm/giantswarm-firstapp-java/blob/master/src/main/java/currentweather/App.java), which contains all our application logic. If you're interested in the internal workings of it, check it's content. It is mainly based on Mattihas' [Getting Started with Java Development on Docker](http://blog.giantswarm.io/getting-started-with-java-development-on-docker), so it also uses Maven and a [pom.xml](https://github.com/giantswarm/giantswarm-firstapp-java/blob/master/pom.xml) to build a fat Jar with all dependencies for deployment. For our tutorial, we won't go into much more details here. If you're working with Java you should quickly realize, how to change this code to fit your own app's needs and get your own custom app running.

## Building our Docker image

We now create a Docker image for our Java server. Here is the `Dockerfile` we use for that purpose:

```Dockerfile
FROM java:8

# Install maven
RUN apt-get update
RUN apt-get install -y maven

WORKDIR /code

# Prepare by downloading dependencies
ADD pom.xml /code/pom.xml
RUN ["mvn", "dependency:resolve"]
RUN ["mvn", "verify"]

# Adding source, compile and package into a fat jar
ADD src /code/src
RUN ["mvn", "package"]

EXPOSE 4567
ENTRYPOINT ["/usr/lib/jvm/java-8-openjdk-amd64/bin/java", "-jar", "target/currentweather-jar-with-dependencies.jar"]
```

As you can see, we use a [Java base image](https://registry.hub.docker.com/_/java/), which uses Java 8. We install [Maven](http://maven.apache.org/) to fetch our dependencies specified in the [pom.xml](https://github.com/giantswarm/giantswarm-firstapp-java/blob/master/pom.xml) and build a fat jar.

The prefetching of Docker images you started a couple of minutes ago should be finished by now. If not, please wait until it's done.

Assuming that your Giant Swarm username is `yourusername`, to build the image for your Docker container, you then execute:

```nohighlight
$ docker build -t registry.giantswarm.io/yourusername/currentweather ./
```

## Testing locally

To test locally before deploying to Giant Swarm, we also need a Redis server. This is very simple to set up, since we can use a standard image here without any modification. Simply run this to start your local Redis server container:

```nohighlight
$ docker run -p 6379:6379 --name=redis -d redis
```

Now let's start the server container, for which we just created the Docker image. Here is the command (replace `yourusername` with your username):

```nohighlight
$ docker run --link redis:redis -p 4567:4567 -ti --rm registry.giantswarm.io/yourusername/currentweather
```

If you want to do this the easy way, you can just use Docker Compose and the [docker-compose.yml](https://github.com/giantswarm/giantswarm-firstapp-java/blob/master/docker-compose.yml). Instead of the two docker commands above you now can do a simple:

```nohighlight
$ docker-compose up
```

By now, it should be running. But we need proof! Let's issue an HTTP request.

Accessing the server in a browser requires knowledge of the IP address your docker host binds to containers. This depends on the operating system.

__Mac/Windows__: with `boot2docker` you can find it  using `boot2docker ip`. The default here is `192.168.59.103`.

__Linux__: the command `ip addr show docker0|grep inet` should print out a line containing the correct address. The default in this case is `172.17.42.1`.

So one of the following two commands will likely work:

```nohighlight
$ curl 192.168.59.103:4567
$ curl 172.17.42.1:4567
```

Your output should look something like this:

```nohighlight
Live weather: The current temperature 17 degrees and the wind is 20.52 km/h.
```

Try at least two requests within 60 seconds to verify your cache is working. Yout logs should say "Querying live weather data" on each non-cached API call.

Awesome. Now let's deploy it to the cloud.

## Bringing it to Giant Swarm

### Uploading to the registry

To use this Docker image on Giant Swarm it has to be uploaded to a Docker registry. Giant Swarm offers you a such a registry. Here we briefly explain how to use it. For more information on using the Giant Swarm registry, see our [registry reference](/reference/registry/).

Before pushing to the registry, you have to log in with the `docker` client. Use this command:

```nohighlight
$ docker login https://registry.giantswarm.io
```

You will be prompted for username, password and email. Use your Giant Swarm account credentials here.

Still assuming that your username is `yourusername`, you can now push the image like this:

```nohighlight
$ docker push registry.giantswarm.io/yourusername/currentweather
```

### Configuring your application

Applications in Giant Swarm are [configured using a JSON configuration file](/reference/swarm-json/) that is usually called `swarm.json`. For this application, we create a configuration containing one service with two components.

Pay close attention to how we create a link between our two components by defining a dependency in the `java` component pointing to the `redis` component.

```json
{
  "app_name": "currentweather",
  "services": [
    {
      "service_name": "currentweather-service",
      "components": [
        {
          "component_name": "java",
          "image": "registry.giantswarm.io/$username/currentweather",
          "ports": [ 4567 ],
          "domains": { "currentweather-$username.gigantic.io": 4567 },
          "dependencies": [
            {
              "name": "redis",
              "port": 6379
            }
          ]
        },
        {
          "component_name": "redis",
          "image": "redis",
          "ports": [6379]
        }
      ]
    }
  ]
}
```

### Starting the application

With the above configuration saved as `swarm.json` in your current directory you can now create and start the application using the `swarm up` command below. As always, replace `yourusername` with your actual username. The flag `--var=username=yourusername` will take care of placing your username in the positions where the `$username` variable is used in `swarm.json`.

```nohighlight
$ swarm up --var=username=yourusername
```

You will see some progress output during creation and startup of your application:

```nohighlight
Creating 'currentweather' in the 'yourusername/dev' environment...
Application created successfully!
Starting application currentweather...
Application currentweather is up.
You can see all services and components using this command:

    swarm status currentweather

```

Seeing is believing, they say. So let's do the final test that your application is actually doing what it should. Open the URL (with your username in the right place) in the browser:

[http://currentweather-yourusername.gigantic.io/](http://currentweather-yourusername.gigantic.io/)

If you watched closely, after starting our app we got the recommendation to check it's status using

```nohighlight
$ swarm status currentweather
```

So here is what we get when doing so (your output will vary slightly):

```nohighlight
App currentweather is up

service                 component  instanceid    created              status
currentweather-service  java       wN2thyTNU69r  2015-03-13 10:23:19  up
currentweather-service  redis      rCRraU1cGunF  2015-03-13 10:23:19  up
```

Here you have them, your two components, running on Giant Swarm. If you want to, you can check the logs using the instance IDs you see in the `swarm status` output. The syntax for the command is `swarm logs <instance-id>`.

Now if you like, you can stop or even delete the application again.

```nohighlight
$ swarm stop currentweather
$ swarm delete currentweather
```

We hope you enjoyed this tutorial. If yes, feel free to tweet and blog it out to the world. If not, please do a PR or let us know what bugged you (see chat and support info at the bottom of this page).

If you're still hungry, why not continue with a more advanced tutorial?

## Further reading

* [Your first application - in your language](/guides/your-first-application/): this guide in other languages
* [Swarmify Ruby on Rails](/guides/ruby_on_rails/) - a simple Rails example involving MySQL
* [Getting started with Docker and MeanJS](http://blog.giantswarm.io/getting-started-with-docker-and-meanjs) - Easily adaptable to Giant Swarm with the experience you have by now.
