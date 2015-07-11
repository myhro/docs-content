+++
title = "Creating and starting an application in one step"
description = "This is the reference page for the 'swarm up' command, which allows you to create and start an application in one step."
date = "2015-07-11"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm up"]
weight = 50
+++

# `swarm up`: Create and Start an Application

The `swarm up` command creates an application and starts it. The command is an alias for the combined use of `swarm create` and `swarm start`, which enables the CLI to block and wait for the application to be created, started and achieve running state.

*Note: When an application is started with the `swarm up` command, it will be located in the same organization and environment being used by the `swarm` client.*

## Command Syntax
The `swarm up` command can be called to create and start an application without any arguments if a valid `swarm.json` file is present in the current working directory:

```nohighlight
swarm up
```

The command can be combined with the `-d` or `--detach` flags to enable non-blocking mode in the CLI when executing the 'starting' portion of the application:

```nohighlight
swarm up --detach
```

Additionally, the command can be called with arguments which specify a JSON-based configuration file:

```nohighlight
swarm up <config_file>
```

#### Example with Response
```nohighlight
$ swarm up swarm.json
Creating 'helloworld' in the 'bant/dev' environment...
Application created successfully!
Starting application helloworld...
\ <--- spinner
Application helloworld is up.
You can see all services and components using this command:

    swarm status helloworld
```

<!-- TODO: Explain what this actually does in the background or alternatively link to the architecture overview article which explains this in more detail. -->

## Using Variables in Options
Application configuration files like `swarm.json` can utilize variables which act as placeholders for values. The values can be passed to the `swarm create` command using two methods, which may be mixed and matched as needed:

 * command line options and/or
 * a variable definition file

### Passing Variables Using the Command Line
Use the `--var=` option to pass variable values on the command line:

```nohighlight
swarm create --var=<key>=<value>
```

Here is an example with response where the `swarm.json` file contains two variables, `$redis_port` and `$domain`, which are populated by using the `--var=` method:

```nohighlight
$ swarm up --var=redis_port=6397 --var=domain=dev.gigantic.io
Creating 'helloworld' in the 'bant/dev' environment...
Application created successfully!
Starting application helloworld...
\ <--- spinner
Application helloworld is up.
You can see all services and components using this command:

    swarm status helloworld
```

*Note: Make certain you include the two distinct equal signs in each option when using this feature!*

### Passing Variables Using a File
Use the `--var-file=` option to pass a JSON formatted file on the command line and a variable named `username`, which is set to `bant`:

```
swarm up --var-file=appvars.json --var=username=bant
```

*Note: The previous example would need to have a `swarm.json` file present in the working directory to function correctly.*

Conversely, if a file named `swarmvars.json` is already present in the *working directory*, a simplified version of the command may be used:

```
swarm up --var=username=bant
```

#### JSON Variable File Format
The JSON formatted variable file is comprised of a single JSON object, the top level of which defines keys that are named after your [environments](/reference/cli/env/). This naming scheme allows you to assign different values to variables for each of your environments.

Here is an example called `swarmvars.json`:

```json
{
    "bant/dev": {
        "redis_port": 8080,
        "domain": "localhost"
    },
    "startup/production": {
        "redis_port": 6397,
        "domain": "dev.gigantic.io"
    }
}
```

Now here's the corresponding `swarm.json` file, utilizing the `$redis_port`, `$domain` and `$username` variables:

```json
{
  "app_name": "swarm-hello",
  "services": [
    {
      "service_name": "webserver",
      "components": [
        {
          "component_name": "python",
          "image": "registry.giantswarm.io/$username/hello",
          "ports": [1337],
          "dependencies": [
            {
              "name": "redis",
              "port": $redis_port
            }
          ],
          "domains": {
            "$domain": 1337
          }
        },
        {
          "component_name": "redis",
          "image": "redis",
          "ports": [$redis_port]
        }
    }
  ]
}
```

Finally, let's take a look at the corresponding commands which set the environment to `bant/dev` and include the `username` variable on the command line. Remember, the remaining variables are drawn from the implicit use of the `swarmvars.json` and `swarm.json` files above:

```nohighlight
swarm env bant/dev
swarm up --var=username=bant
```

For further information about the app configuration file, please refer to the [application configuration reference page](/reference/swarm-json/).

*Note: The order of the command line options is not important.*

## Further Reading
* [Creating an Application](/reference/cli/create/)
* [Starting an Application](/reference/cli/start/)
* [Application Configuration](/reference/swarm-json/)
