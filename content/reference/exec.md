+++
title = "Execute a command in a component instance"
description = "This is the reference page for the 'swarm exec' command, which allows you to call a command inside an instance of a component or open a shell."
date = "2015-05-08"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm exec"]
weight = 40
+++

# Executing a Command in a Component Instance

With the `swarm exec` command, you can start the execution of a new process in a running instance of a Giant Swarm application component. This allows to run a script or start an interactive shell, depending on what capabilities the instance provides.

## Basic Syntax

```nohighlight
$ swarm exec <instance-id> [-d|--detach] [--] [<command>]
```

* The `instance_id` argument specifies an instance of a running application component. Use the [`swarm status`](../status/) command to list all instances of an application. Note that you can use partial IDs (ommitting characters from the end) as long as the part is unique within your environment.

* When the optional `-d` or `--detach` flag is used, the CLI will return immediately after the command has been started on the instance, not waiting for the end of the execution.

* The seperator `--` has to be used when a `command` argument containing whitespace shall be used.

* The `command` argument specifies the command to be executed in the target instance. If no command argument is given, the command `/bin/sh` is used to open a shell into the container.

## Examples

Assuming that we have an instance with the ID `pyhna3vqp24g`, we can quickly list the content of the current directory using this command:

```nohighlight
$ swarm exec pyhna3vqp24g ls
```

Of course, this requires the `ls` binary to be existent in the running container and accessible in the PATH, which is entirely in your responsibility.

Passing additional arguments to a command is possible, too, but this requires the seperator `--` to be present to prevents the CLI from trying to parse the additional arguments.

So executing `ls -la` on your target instance takes this command:

```nohighlight
$ swarm exec pyhna3vqp24g -- ls -la
```

Both examples shown above will wait for the result of the command execution and print the STDOUT and STDERR output to your terminal. To prevent this, use the `-d` or `--detach` flag.

You could start a long running process this way:

```nohighlight
$ swarm exec pyhna3vqp24g -d /path/to/tedious-task
```

## Running a Shell

Using exec allows you to open an interactive shell into your instance, given that the container you are running actually contains a shell executable, which should be the case with most standard Linux containers.

The syntax for calling a shell executable is exactly the same as for other commands, as described above.

If you have Bash in `/bin/bash`, here is how you can run it:

```nohighlight
$ swarm exec <instance_id> /bin/bash
```

If you like it even more reduced and have `/bin/sh`, you don't need to specify the command at all. Simply call

```nohighlight
$ swarm exec <instance_id>
```

Quitting a shell session usually requires the `exit` command or hitting `Ctrl + D`.

## Considerations

With the ability to execute commands interactively comes the question: When to use this function and when to rely on the main process started using the `ENTRYPOINT` or `CMD` property of a Docker container or their counterparts in the application configuration?

Docker containers are designed for running one process, and Giant Swarm is building on this design decision. When the main process initiated in a container dies, the container is restarted. Only the main process' log output is gathered via Giant Swarm's global log aggregation.

As a consequence, whenever you want to rely on a process actually being running, you should always make this the main process in a container.

So `swarm exec` is basically suited for short-running processes. This could be jobs like importing data into a database, manually creating a backup of some directory and sending this somewhere or interactively debugging a system while it's running.

## Limitations

Currently, the return values of executed commands are not reflected when executing `swarm exec`. Also, there is no way to determine from the `swarm exec` exit code whether a command could even be executed or not (for example since it doesn't exist).

## Further Reading

* [Getting an applications's status](../status/) and listing instance IDs available in an application
* [Getting statistics of an application, service, component or instance](../stats/)
