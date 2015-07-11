+++
title = "Stopping an application or service"
description = "The reference page for the 'swarm stop' command, which is used to stop entire applications or specific services."
date = "2015-07-11"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm stop"]
weight = 60
+++

# `swarm stop`: Stopping Applications or Services
The `swarm stop` command is used to stop a specific service's or application's instances. These instances may be restarted at a later time using the [`swarm start`](/reference/cli/start) command.

When an application is stopped with `swarm stop`, all service component instances are stopped. 

The command can be called to stop an entire application without any arguments if a valid `swarm.json` file is present in the current working directory and represents a running application with the same name, in the current organization and environment as reported by `swarm env`:

```nohighlight
swarm stop
```

The CLI will normally block and wait for the application to stop before it returns control to the shell. The command can also be issued in non-blocking mode using the `-d` or `--detach` flags:

```nohighlight
swarm stop --detach
```

The command can also be passed an application name:

```nohighlight
swarm stop <app_name>
```

#### Example with Response
```nohighlight
$ swarm stop
Stopping application helloworld...
\ <--- spinner
Application helloworld is down
```

<i class="fa fa-exclamation-triangle"></i> *Note: When a component instance is stopped, the __data stored inside the instance is lost__. Data persistance through stops and starts may be achieved by using [volumes](/reference/swarm-json/#volumes).*


## Stopping a Service
The `swarm stop` command may be used to stop a service (a set of components) inside a given application without stopping the other services in that application. A single service is stopped by using the application name, a `/` separator and the service name:

```nohighlight
swarm stop <app_name>/<service_name>
```

## Further reading

* [Starting an Application or Service](/reference/cli/start/)
* [Application Configuration](/reference/swarm-json/)
* [Global Command Line Options](/reference/cli/global-options/)
