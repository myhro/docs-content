+++
title = "Starting an application or service"
description = "This is the reference page for the 'swarm start' command, which allows you to start an application or service."
date = "2014-12-11"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm start"]
weight = 50
+++

# `swarm start`: Start Application or Service

Applications and services inside applications are started by using the `swarm start` command.

Before an application can be started, it must be created by using the [`swarm create`](/reference/cli/create/) command using a specific [environment](/refernce/cli/env/) setting.

## Command Syntax
The `swarm start` command can be called to start an application without any arguments if its corresponding `swarm.json` file is present in the current working directory:

```nohighlight
swarm start
```

#### Example with Response
```nohighlight
$ swarm start
Starting application swarm-hello...
Application swarm-hello is up.
```

*Note: The CLI will block until the application reaches `running` state unless the `--detach` option is set.*

### Command Options
The `swarm start` command can be called with an optional `-d` or `--detach` flag to prevent blocking:

```nohighlight
swarm start --detach
```

Application services may be started in whole or individually. All services may be started together by specifying the application name:

```nohighlight
swarm start <app_name>
```

Services may be started individually by specifying the service name in addition to the application name:

```nohighlight
swarm start <app_name>/<service_name>
```

Here is an example where the `webserver` portion of the application is started in non-blocking mode:

```nohighlight
$ swarm start -d swarm-hello/webserver
```

*Note: If the `swarm start` command is issued in a directory with a valid `swarm.json` configuration file, the entire application will be started.*

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
