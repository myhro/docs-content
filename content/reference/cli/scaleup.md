+++
title = "Scaling up a component"
description = "Reference page for the 'swarm scaleup' command, which allows you to increase the number of instances running for a particular component."
date = "2015-07-14"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm scaleup"]
weight = 90
+++

# `swarm scaleup`: Scale Up a Component

The `swarm scaleup` command is used to increase the number of service component instances.

## Command Syntax

The `swarm scaleup` command is called using service component name as argument and the number of instances to add for the component. If a number of instances is not given, the number of instances is scaled up by one (1) instance.

```nohighlight
swarm scaleup <app/service/component> <num_to_add>
```

#### Example with Response

```nohighlight
$ swarm scaleup currentweather/currentweather-service/flask 2
Scaling up component currentweather/currentweather-service/flask by 2...
```

*Note: The `swarm scaleup` command is affected by the current Giant Swarm [service limits](https://giantswarm.io/limits/).*


## Further reading

 * [Scaling Down a Component](/reference/cli/scaledown/)
