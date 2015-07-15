+++
title = "Scaling down a component"
description = "Reference page for the 'swarm scaledown' command, which allows you to reduce the number of instances running for a particular component."
date = "2015-01-12"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm scaledown"]
weight = 91
+++

# `swarm scaledown`: Scale Down a Component

The `swarm scaledown` command is used to decrease the number of service component instances.

## Command Syntax

The `swarm scaledown` command is called using service component name as argument and the number of instances to remove form the component. If a number of instances is not given, the number of instances is scaled down by one (1) instance.

```nohighlight
swarm scaledown <app/service/component> <num_to_remove>
```

*Note: The `swarm scaledown` command has no effect when a component only has a single running instance.*

#### Example with Response

```nohighlight
$ swarm scaledown currentweather/currentweather-service/flask 1
Scaling down component currentweather/currentweather-service/flask by 1...
```

## Further reading
 * [Scaling Up a component](/reference/cli/scaleup/)
