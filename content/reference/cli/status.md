+++
title = "Getting an applications's status"
description = "Reference page for the 'swarm status' command, which prints out the status of a particular application and its services and components."
date = "2015-07-14"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm status"]
weight = 84
+++

# `swarm status`: View Application Status

The `swarm status` command returns information about a specific application and its services and components.

## Command Syntax
The `swarm status` command is called using the application name, which is defined in the `swarm.json` file using the `app_name` key when you start the application.

```nohighlight
swarm status <app_name>
```

*Note: Use `swarm ls` to get a listing of all applications in the current environment.*


#### Example with Response
*Note: This example uses truncated output for improved readablity.*
```nohighlight
$ swarm status weather
App weather is up

service          component  image             instanceid    created              status
weather-service  flask      weather:latest    1ifd72dklx8z  2015-07-14 23:05:11  up
weather-service  flask      weather:latest    wz2umflmmy3t  2015-07-15 01:06:06  up
weather-service  redis      redis             ft2gnsw59oj4  2015-07-14 23:03:06  up

```

## Response Explained
The first line of the output shows the status of the application as a summary. This status is reported as the *worst* status of the application's components. For example, if one component is `down`, the entire application will be considered `down`.

The remaining lines of output shows a table of components listed by the application's services. The table columns show which service the component belongs to, the component name, the ID of the instance the component is running on, the date and time when the instance of the component was first started, and the component status. If a component is running on more than one instance, each instance is represented in an individual row.

### Instance Status Explained
An application's component instances may be in one of the following states:

 * `up`: The component has an instance running.
 * `starting`: The component has an instance starting.
 * `down`: The component does not have an instance running.
 * `failed`: The component is does not have an instance running and, unfortunately, there was an error.

## Further Reading

* [List applications](/reference/cli/ls/)
* [Accessing process logs](/reference/cli/logs/)
* [Getting instance statistics](/reference/cli/stats/)
* [Getting basic information](/reference/cli/info/)
