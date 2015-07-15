+++
title = "Getting statistics for one or several instances"
description = "Reference page for the 'swarm stats' command, which allows you to access basic statistics about the resource usage of your components."
date = "2015-07-14"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm stats"]
weight = 86
+++

# `swarm stats` View Application Statistics
The `swarm stats` command returns usage information for an application, service, component or instance. The command takes a variety of application, service, component and instance names.

## Command Syntax
The `swarm stats` command is called by using the application name:

```nohighlight
swarm stats <application_name>
```

#### Example with Response
```nohighlight
$ swarm stats currentweather
service                 component  instanceid    status  memory usage (mb)  memory capacity (mb)  memory usage (%)  cpu usage (%)
weather-service  flask      gg0tu0al5uya  up      27.93              512.00                5.45              0.01
weather-service  redis      ft2gnsw59oj4  up      8.31               512.00                1.62              0.07
```

The `swarm stats` command can also be called on a service by appending the service name to the application name:

```nohighlight
swarm stats <application_name>/<service_name>
```

Finally, the `swarm stats` command can be called on a component by appending the component name to the application and service names:

```nohighlight
swarm stats <application_name>/<service_name>/<component_name>
```

#### Example with Response
```nohighlight
$ swarm stats currentweather/weather-service/redis
service          component  instanceid    status  memory usage (mb)  memory capacity (mb)  memory usage (%)  cpu usage (%)
weather-service  redis      ft2gnsw59oj4  up      8.31               512.00                1.62              0.07
```

### Viewing a Specific Instance's Statistics
The `swarm stats` command can alternately be called with an instance ID to return usage information for a give component instance:

```nohighlight
swarm stats <instance_id>
```

#### Example with Response
```nohighlight
$ swarm stats gg0tu0al5uya

service                 component  instanceid    status  memory usage (mb)  memory capacity (mb)  memory usage (%)  cpu usage (%)
currentweather-service  flask      gg0tu0al5uya  up      27.93              512.00                5.45              0.01
```

*Note: The easiest way to return all instance IDs for a given application is by doing a `swarm status <app_name>`.*


## Further Reading

 * [Getting an Application's Status](/reference/cli/status/)
 * [Accessing Instance Logs](/reference/cli/logs/)

