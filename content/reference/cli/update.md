+++
title = "Updating a component"
description = "This is the reference page for the 'swarm update' command, which allows you to update a component to run a newer version of a container."
date = "2015-06-30"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm update"]
weight = 70
+++

# `swarm update`: Update a Component with Image

The `swarm update` command is used to update a component's container to use a specific version of an image. The update stops the component's instance(s), pulls the desired image from the registry, and then restarts the instance(s) using the new image.

The component update can be done using one of three optional rolling [update strategies](#updatestrategy):

* `one-by-one`(default)
* `all-at-once`
* `hot-swap`

*Note: A component's instance will be started if it was not running before the `swarm update` command was issued.*

## Command Syntax
The `swarm update` command is called using the application, service and component name to update the component image with the latest image:

```nohighlight
swarm update <app>/<service>/<component>
```

When a `swarm update` is issued to a component, it will use the `:latest` tag from the Docker index **by default**. Tags may be set by using `docker push` and prepending a version using `:<version_tag>`.

#### Example with Response
This example first pushes an image to the registry tagged with `latest`, then updates the `currentweather/currentweather-service/flask` component to the latest version for that image:

```nohighlight
$ sudo docker push registry.giantswarm.io/bant/currentweather-python:latest
The push refers to a repository [registry.giantswarm.io/bant/currentweather-python] (len: 1)
Sending image list
Pushing repository registry.giantswarm.io/bant/currentweather-python (1 tags)
Image bf84c1d84a8f already pushed, skipping
...
4b8d5ad81a18: Image successfully pushed
Pushing tag for rev [4b8d5ad81a18] on {https://registry.giantswarm.io/v1/repositories/bant/currentweather-python/tags/latest}

$ swarm update currentweather/currentweather-service/flask
Updating component 'currentweather/currentweather-service/flask' with version 'latest' ...
```

## Update with a Versioned Image {#versionedimage}
The `swarm update` command can also be called using the application, service, component and *version* named tag:

```nohighlight
swarm update <app>/<service>/<component> <version_tag>
```

#### Example with Response
This example first pushes an image to the registry tagged with `2.7.3`, then updates the `currentweather/currentweather-service/flask` component to use the `2.7.3` version for that image:

```nohighlight
$ sudo docker push registry.giantswarm.io/bant/currentweather-python:2.7.3
The push refers to a repository [registry.giantswarm.io/bant/currentweather-python] (len: 1)
Sending image list
Pushing repository registry.giantswarm.io/bant/currentweather-python (1 tags)
Image cb5666ced332 already pushed, skipping
...
55bf9745b7af: Image successfully pushed
Pushing tag for rev [55bf9745b7af] on {https://registry.giantswarm.io/v1/repositories/bant/currentweather-python/tags/2.7.3}

$ swarm update currentweather/currentweather-service/flask
Updating component 'currentweather/currentweather-service/flask' with version 'latest' ...
\ <--- spinner
$
```

## Update with a Specific Strategy {#updatestrategy}

A rolling update strategy can be specified when upgrading a component. The strategy can be set by adding the `-s` or `--strategy` flag, followed by the desired update strategy which can be one of `one-by-one`, `all-at-once` or `hot-swap`:

```nohighlight
swarm update -s one-by-one <app>/<service>/<component> <version_tag>
```

#### Example with Response
```nohighlight
$ swarm update -s hot-swap currentweather/currentweather-service/flask 2.7.3
Updating component 'currentweather/currentweather-service/flask' with version '2.7.3' and strategy 'hot-swap'...
\ <--- spinner
$ 
```

## Further Reading

* [Using the registry](/reference/registry/)
* [Application configuration](/reference/swarm-json/)
