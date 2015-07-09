+++
title = "Getting basic information"
description = "Reference page for the `swarm info` command, which allows you to get some basic information on your login and environment status."
date = "2015-01-29"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm info"]
weight = 80
+++

# `swarm info`: Get Basic Information

The `swarm info` command provides information about a Giant Swarm account, including cluster status, CLI verson, logged in user and the current enviroment.

## Command Syntax

The `swarm info` comand is called without any arguments by doing:

```nohighlight
swarm info
```

#### Example with Response

```nohighlight
$ swarm info
Cluster status:      reachable
Swarm CLI version:   0.17.0
Logged in as user:   bant
Current environment: bant/dev
```

### Response Explained

* `Cluster status` - The general platform health, normally `reachable`.
* `Swarm CLI version` - The version of the installed command line client.
* `Logged in as user` - The current logged in username. 
* `Current environment` - The current working environment.

*Hint: If you are only interested in the current user name, you can use the [`swarm user`](/reference/cli/user/) command. The same is true of the current environment, which can be retrieved using [`swarm env`](/reference/cli/env/).*

## Further Reading

* [Environments](/reference/cli/env/)
* [Organizations](/reference/cli/org/)
