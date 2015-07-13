+++
title = "Scaling up a component"
description = "Reference page for the 'swarm scaleup' command, which allows you to increase the number of instances running for a particular component."
date = "2015-01-12"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm scaleup"]
weight = 90
+++

# Scaling up a component

The `swarm scaleup` command is used to increase the number of instances running a service component. Note that scaled instances of a component with volumes do not share those volumes between eachother, but each have their own volumes.

If you scale a component in a [`pod`](/reference/swarm-json/#pod) all components in that group will be scaled. Note that each group of scaled instances will have their own namespaces and can be scheduled on different machines. Thus, each group of component instances is only part of their respective namespaces on one host, instances on different hosts do not share namespaces.

## Command syntax

The command requires a service component name as argument. Optionally, the number of instances to be added can be given. If no number is given, `1` is assumed.

```nohighlight
$ swarm scaleup <app/service/component> [num-to-add]
```

For example, if you have an "onlineshop" app with a service called "imageserver" and a component called "nginx", use the following command to __add one__ instance for this component:

```nohighlight
$ swarm scaleup onlineshop/imageserver/nginx
```

To add two new instances, use this syntax:

```nohighlight
$ swarm scaleup onlineshop/imageserver/nginx 2
```

## Further reading

 * [Scaling down a component](/reference/cli/scaledown/)
 * [Scaling policy in swarm.json](/reference/swarm-json/#scaling_policy)
 * [Scaling pods](/reference/swarm-json/#pod)
