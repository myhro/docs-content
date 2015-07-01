+++
title = "Introduction to Giant Swarm"
description = "A general introduction into what Giant Swarm is and does. Start here if you are interested in using our service."
date = "2015-05-21"
type = "page"
+++

# Introduction to Giant Swarm

With Giant Swarm you can build deploy and manage your containerized applications. There are no restrictions regarding programming languages, frameworks, or databases. We are especially optimized for applications that are built in a microservices style. We offer an opinionated solution, but maintain the flexibility to change the tooling to your own liking and needs.

A Swarm application consists of an application configuration (`swarm.json`) and one or more Docker containers. To deploy and start an application you use the `docker` client to build and push containers and `swarm` to push the application configuration as well as manage your application:

![](/img/overview.png)

## What is Docker?

> Self-sufficient linux containers.
 
Docker is the most popular application container format. It lets you package your own stack in lightweight, portable, and self-sufficient manner. Some call it therefor "lightweight VM". In contrast to traditional VM's it's an order of magnitude faster, lighter, and more fun. ;-)

Docker makes a great tool for developers since it allows you to package different parts of your applications in your own containers. These containers can run on different environments like your and your colleagues notebook as well as different server environments like Giant Swarm.

Another great aspect of Docker is that there are lots of predefined containers available. From different languages like JavaScript to Golang over different webstacks like Spring to Rails down to databases like Redis or MySQL. In addition defining your own Docker container is very easy.

To learn more about Docker and how to use it, see our [Introduction to Docker](/guides/introduction-to-docker/).

## What is a Giant Swarm application?

> Orchestrated services backed by Docker containers

A Giant Swarm application consists of one or more services. These form logical units in your application describing domain or technical services. Examples would be a `user-service` or a `redis-service.` 

Each service itself consists of one or more components. A component is backed by a Docker container by referencing its image. These images can be one of the many predefined images from the [Docker Hub](https://registry.hub.docker.com/) or can be built by yourself. If you follow our [Your first application](/guides/your-first-application/) tutorial, you will define your first Docker container within the next half an hour or less.

## What is the swarm.json?

> The Giant Swarm application configuration

Your Giant Swarm application is described and configured with a simple configuration file called: `swarm.json`. 

Look at this simple example of a Giant Swarm application consisting of two services: The first service has one component and the second service has two components. Component-a and component-x use userdefined Docker containers. Component-y uses the standard redis container.

![](/img/overview-app-service-component.png)

A `swarm.json` would look something like:

```json
{
    "app_name": "application",
    "services": [
        {
            "service_name": "service1",
            "components": [
                {
                    "component_name": "component-a",
                    "image": "registry.giantswarm.io/giantswarm/customimage",
                    "ports": [ 1337 ],
                    "domains": { "helloworld.gigantic.io": 1337 }
                }
            ]
        },
        {
            "service_name": "service2",
            "components": [
                {
                    "component_name": "component-x",
                    "image": "registry.giantswarm.io/giantswarm/customimage",
                    "ports": [ 1337 ],
                    "dependencies": [
                        { "name": "component-y", "port": 6379 }
                    ]                
                },
                {
                    "component_name": "component-y",
                    "image": "redis",
                    "ports": [ 6379 ]
                }
            ]
        }
    ]
}
```

## The Swarm CLI

> A simple binary to control your Swarm apps

The main interface to Giant Swarm is a CLI called `swarm`. After you have been authenticated you can create apps; start, stop, and update them and monitor them. A small example `swarm ls` lists the current installed apps on Giant Swarm:  

```nohighlight
$ swarm ls
2 applications available:

application     environment  created              status
currentweather  luebken/dev  2014-11-28 14:01:35  up
helloworld      luebken/dev  2014-11-28 14:01:43  up
```

## Microservices

The days of big monolith applications are long over. Modularizing your applications in services has been the way to go for some time. Each self-contained functionality constitutes a separate service. This makes them independently developable, deployable, and scalable. Each with its own potential database and programming language. The right tool for the job! The [12factor Apps](http://12factor.net/) paradigm goes even further by describing twelve fundamental requirements of modern Software-as-a-Service apps - worth looking at.

Giant Swarm helps you creating apps in microservice fashion. Each service is truly separate and independently developable, deployable, and scalable. In the end it's up to you how much or little you put in a service.

## Where to go from here?

* If you don't have an account for Giant Swarm yet, [request an invite](https://giantswarm.io/) now. We have a waiting list and the sooner you get on the list, the sooner you will be able to start using Giant Swarm.

* Once you have an account, check that everything is working fine using the Quick Start on the [documentation start page](/).

* Then we recommend to create [Your first application - in your language](/guides/your-first-application/) on Giant Swarm.

## Further Reading

If you like to read (hear or watch) some more about Docker and Microservices, here are some recommendations from our side.

### Docker

* [Introduction to Docker](/guides/introduction-to-docker/) - our own introductory Docker tutorial

* [Docker Documentation](https://docs.docker.com/) - The official Docker docs

* [The Docker Book](http://www.dockerbook.com/) - For book lovers, by James Turnbull

### Microservices

* [Introduction to Microservice Architectures](https://giantswarm.io/microservices/) - Our own short introduction to microservices

* [Microservices](http://martinfowler.com/articles/microservices.html) - The widely-shared introduction article by Martin Fowler and James Lewis

* [Building Microservices](http://shop.oreilly.com/product/0636920033158.do) - For book lovers, by Sam Newman

* [State of the Art in Microservices](https://www.youtube.com/watch?v=nMTaS07i3jk) - Presentation on DockerCon 14|EU by Adrian Cockcroft (Video)

* [Architecture and Micro Services](https://www.innoq.com/de/links/microservices-se-radio/) - Software Engineering Radio Podcast with Stefan Tilkov
