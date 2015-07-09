+++
title = "swarm CLI Reference"
description = "This is the reference page for the Giant Swarm command line interface (CLI)."
date = "2015-05-18"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
weight = 1
+++

# The swarm CLI
The `swarm` CLI is a command line client used to deploy, query and control your Giant Swarm applications.

## Getting Started
Please refer to the following guides to get started using Giant Swarm's `swarm` client:

* [Installation and Configuration](installation/) - instructions on how to set up the CLI on OS X and Linux.
* [Global Command Line Options](global-options/) - options for working with CLI commands.
* [Release Notes](release-notes/) - information about changes in the CLI releases.

## Command Reference
Click on the links below for more details on a particular command usage:

Command | Description
--------|---------------------
[`swarm login`](login/) | Sign in with your Giant Swarm credentials
[`swarm info`](info/) | Get basic information on context and environment
[`swarm ls`](ls/) | List all applications
[`swarm create`](create/) | Define a new application by loading a JSON file
[`swarm start`](start/) | Start an application or service
[`swarm up`](up/) | Define and start a new application
[`swarm stop`](stop/) | Stop an application or service
[`swarm update`](update/) | Update a single component of an application
[`swarm status`](status/) | Show current status of an application or service
[`swarm scaleup`](scaleup/) | Increase number of instances running a component
[`swarm scaledown`](scaledown/) | Reduce number of instances running a component
[`swarm logs`](logs/) | Print logs from a component instance
[`swarm stats`](stats/) | Print basic statistics of an instance
[`swarm env`](env/) | Handle environments
[`swarm cat`](cat/) | Show the configuration of an application
[`swarm user`](user/) | Create and modify users
[`swarm org`](org/) | Manage organizations
[`swarm exec`](exec/) | Execute commands on a component instance
`swarm delete` | Delete an app. Note that you lose all contained data by doing so. See `swarm help delete` for details.
`swarm version` | Print swarm client version. See `swarm help version` for details.
`swarm logout` | Log out from your account. See `swarm help logout` for details.
`swarm completion` | Setup CLI completion. See `swarm help completion` for details.
