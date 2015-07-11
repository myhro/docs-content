+++
title = "Starting an application or service"
description = "This is the reference page for the 'swarm start' command, which allows you to start an application or service."
date = "2015-07-11"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm start"]
weight = 50
+++

# `swarm start`: Start Application or Service

The `swarm start` command is used to start an application or a service inside an application after it has been created using the [`swarm create`](/reference/cli/create/) command.

*Note: To start the applicaiton, the `swarm start` command will need to be using the same organization and environment used by the `swarm create` command to create the application.*

## Command Syntax

The `swarm start` command can be called to start an application without any arguments if a valid `swarm.json` file is present in the current working directory:

```nohighlight
swarm start
```

The CLI will normally block and wait for the application to start before it returns control to the shell. The command can be issued in non-blocking mode using the `-d` or `--detach` flags:

```nohighlight
swarm start --detach
```

The command can also be passed an application name:

```nohighlight
swarm start <app_name>
```

#### Example with Response
```nohighlight
$ swarm start -d helloworld
Starting application helloworld...
To check the status of your application, use

	swarm status helloworld
```

## Starting a Service
The `swarm start` command may be used to start a service (a set of components) inside a given application without starting the other services in that application. A single service is started by using the application name, a `/` separator and the service name:

```nohighlight
swarm start <app_name>/<service_name>
```

#### Example with Response
```nohighlight
$ swarm start -d helloworld/webserver
Starting service helloworld/webserver...
To check the status of your service, use

	swarm status helloworld/webserver
```

## Combining Commands
The `swarm start` command can be used in conjunction with the `swarm ls` and `swarm status` commands to start and monitor an application and its services.

Here is an example where an application is listed and then started using a combination of those commands:

```nohighlight
$ swarm ls
1 application available in environment 'bant/dev':

application           created              status
swarm-hello           2015-05-30 18:53:38  down

$ swarm start swarm-hello
Starting application swarm-hello...
\ <--- spinner
To check the status of your application, use

	swarm status swarm-hello

$ swarm status swarm-hello
App swarm-hello is up

service   component image                             instanceid   created          status
webserver python    registry.giantswarm.io/bant/hello tbqvm3hqti7d 2015-05-30 18:53 up
```

## Further Reading

* [Environments](/reference/cli/env/)
* [Application configuration reference (`swarm.json`)](/reference/swarm-json/)
* [Global command line options](/reference/cli/global-options/)
