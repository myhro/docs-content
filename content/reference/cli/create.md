+++
title = "Creating an application"
description = "This is the reference page for the 'swarm create' command, which allows you to create a new application based on your configuration."
date = "2015-07-11"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm create"]
weight = 50
+++

# `swarm create`: Create an Application

Applications on Giant Swarm are created by using the `swarm create` command and a JSON formated configuration file describing the application.

## Command Syntax

The `swarm create` command can be called without any arguments if a file named `swarm.json` is present in the current working directory:

```nohighlight
swarm create
```

The `swarm create` command can also be called with arguments which specify a specific configuration file:

```nohighlight
swarm create <filename>
```

#### Example with Response
```nohighlight
$ swarm create swarm.json
Creating 'helloworld' in the 'bant/dev' environment...
Application created successfully!
```

For further information about the app configuration file, please refer to the [swarm.json reference page](/reference/swarm-json/).

<!-- TODO: Explain what this actually does in the background or alternatively link to the architecture overview article which explains this in more detail. -->

## Using Variables in Options
Application configuration files can utilize variables which act as placeholder keys. The values can be passed to the `swarm create` command using two methods, which may be mixed and matched as needed:

 * command line options and/or
 * a variable definition file

### Passing Variables Using the Command Line
Use the `--var=` option to pass variable values on the command line:

```nohighlight
swarm create --var=<key>=<value>
```

Here is an example where the `swarm.json` file contains two variables, `$redis_port` and `$domain`, which are populated by using two `--var=` options after the `swarm create` command:

```nohighlight
$ swarm create --var=redis_port=6397 --var=domain=dev.gigantic.io
```

*Note: Make certain you include the two distinct equal signs in each option when using this feature!*

### Passing Variables Using a File
Use the `--var-file=` option to pass the name of a JSON formatted file:

```
swarm create --var-file=<filename>
```

Here is an example where a variable file `appvars.json` and a variable named `username` are used together in a single statement:

```
$ swarm create --var-file=appvars.json --var=username=bant
```

*Note: The previous example would need a `swarm.json` file present in the working directory.*

Conversely, if a file named `swarmvars.json` is already present in the *working directory*, you may use the simplified version of the command:

```
$ swarm create --var=username=bant
```

#### JSON Variable File Format
The `JSON` formated variable file format is comprised of a single JSON object, the top level of which defines keys that are named after your [environments](/reference/cli/env/). This naming scheme allows you to assign different values to variables for each of your environments.

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

Now here's the corresponding `swarm.json` file, with variables:

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

Here's an example command which sets the environment to `bant/dev` and includes the `username` variable on the command line. Remember, the remaining variables are drawn from the implicit use of the `swarmvars.json` and `swarm.json` files above:

```nohighlight
$ swarm env bant/dev
$ swarm create --var=username=bant
```

If the configuration file and variable file were named differently, the usage would be slightly more verbose:

```nohighlight
$ swarm env bant/dev
$ swarm create swarm_app.json --var-file=swarm_app_vars.json --var=username=bant
```

*Note: The order of command line options is not important.*

## Further reading
* [Application Configuration Reference (`swarm.json`)](/reference/swarm-json/)
* [Global Command Line Options](/reference/cli/global-options/)
