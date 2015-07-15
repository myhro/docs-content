+++
title = "Environments"
description = "This is the reference page for the 'swarm env' command, which allows you to select, create, and delete environments."
date = "2015-07-14"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm env"]
weight = 40
+++

# `swarm env`: Manage Environments

Giant Swarm environments provide separate contextual namespaces in which you can deploy your applications. A simple example illustrates creating and switching to the `prod` environment namespace for an [organization](/reference/cli/org) named `startup`:

```nohighlight
$ swarm org create startup
Organization 'startup' has been created
$ swarm env startup/prod
Environment startup/prod has been selected
```

When a user signs up for Giant Swarm, they are assigned an organization which carries the same name as their username. For example, a user named `bant` will find they have an organization named `bant` in their account:

```nohighlight
$ swarm user
bant
$ swarm org --list
bant
```

*Note: Organization names are managed using the [`swarm org`](/reference/cli/org/) command. Organization names are unique across the Giant Swarm shared public cluster. If you receive an error creating an organization, try a different name!*

## Command Syntax
### Viewing Environments
The `swarm env` command is used without parameters to view the current organization and environment:

```nohighlight
$ swarm env
bant/dev
```

The `swarm env` command can also be called using the full environment path, which requires an organization name and desired environment name:

```nohighlight
swarm env <org_name>/<env_name>
```

*Note: If the given environment namespace does not exist, it will be created before the CLI switches to using the specified environment path.*

### Listing Environments
The `swarm env` command can be called with the `-l` or `--list` flags to return a list of all available environments for the current user:

```nohighlight
$ swarm env -l
   bant/dev
 * startup/prod
   startup/test
```

### Deleting Environments
The `swarm env` command can be called with the `-d` or `--delete` flag to delete a specific environment:

```nohighlight
$ swarm env -d startup/test
Environment startup/test has been deleted
```

Note: Deleting an environment does not delete or stop any applications deployed in the environment's scope. This means it is entirely possible to 'strand' applications, where you can't stop or delete them. Be careful out there!

## Further Reading

 * [Managing Organizations](/reference/cli/org/)



