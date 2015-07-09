+++
title = "Listing applications"
description = "The reference page for the 'swarm ls' command, which is used to show a list of applications in an environment."
date = "2015-01-29"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm ls"]
weight = 80
+++

# `swarm ls`: List Applications

The `swarm ls` shows a list of applications and their status for the current selected enviroment.

## Command Syntax

The command `swarm ls` is called without any arguments by doing:

```nohighlight
swarm ls
```

#### Example with Response
```nohighlight
$ swarm ls
2 applications available in environment 'bant/dev':

application      environment  created              status
helloworld       bant/dev     2015-05-09 11:07:45  up
twofishes        bant/dev     2015-05-18 06:43:11  down
```

### Response Explained

 * `application` - The application name as defined in the application configuration.
 * `environment` - The name of the environment in which the application is running.
 * `created` - The application creation datetime in UTC.
 * `status` - The status of the application.

### Status Explained

The application status is an aggregate status of of all component instances within an application. The possible statuses are:

 * `up` - All component instances are running.
 * `starting` - One or more of the component instances are in `starting` state.
 * `down` - One or more of the component instances are currently `down`.
 * `failed` - At least one of the components instance are curently in a `failed` state.

## Further Reading

* [Application, Service and Component Status](/reference/cli/status/)
* [Environments](/reference/cli/env/)
