+++
title = "Accessing process logs"
description = "This is the reference page for the 'swarm logs' command, which allows you to access the logs of your component instances."
date = "2015-07-14"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm logs"]
weight = 87
+++

# `swarm logs`: View Instance Logs
The `swarm logs` command returns any output written to `STDOUT` or `STDERR` by a process running inside an instance. There are few things to note about obtaining logs from instances:

* Processes must log to STDOUT or STDERR to be accessible via the `swarm` CLI or [API log methods](/reference/api/#LogInstance).
* [Scaling down](/reference/cli/scaledown/) components and [deleting applications](/reference/cli/exec/) will result in the loss of instance logs.
* Logs older than 14 days will be deleted by a slightly drunk beaver named *J. Edgar*.

## Command Syntax
The `swarm logs` command is called with a given component instance ID:

```nohighlight
swarm logs <instance-id>
```

#### Example with Response
```nohighlight
$ swarm logs gg0tu0al5uya
2015-07-15 01:17:04.672 +0000 UTC - * Running on http://0.0.0.0:5000/
2015-07-15 03:28:23.575 +0000 UTC - 172.17.42.1 - - [15/Jul/2015 03:28:23] "GET /favicon.ico HTTP/1.1" 404
2015-07-15 03:28:26.007 +0000 UTC - Querying live weather data
2015-07-15 03:28:26.224 +0000 UTC - 172.17.42.1 - - [15/Jul/2015 03:28:26] "GET / HTTP/1.1" 200
```

### Requesting Trailing Log Lines
The `swarm logs` command can be instructed to return the trailing set of an instance's log lines using the `--tail` or `-t` flags:

```nohighlight
swarm logs <instance-id> -t <num_lines>
```

#### Example with Response
```nohighlight
$ swarm logs gg0tu0al5uya -t 1
2015-07-15 03:28:36.634 +0000 UTC - 172.17.42.1 - - [15/Jul/2015 03:28:36] "GET / HTTP/1.1" 200
```

### Request All Log Lines
The `swarm logs` command can also be instructed to return the entire set of logs by using the `all` directive following the `--tail` or `-t` flags:

```nohighlight
swarm logs <instance-id> -t all
```

### Request a Continous Stream of Logs
The `swarm logs` command can stream logs to the console by using the `--follow` or `-f` flags:

```nohighlight
swarm logs <instance-id> -f
```

## Further Reading

 * [Getting a List of Instances for an Application](/reference/cli/status/)
 * [Getting an Application's Statistics](/reference/cli/stats/)
