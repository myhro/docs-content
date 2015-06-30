+++
title = "Updating a component"
description = "This is the reference page for the 'swarm update' command, which allows you to update a component to run a newer version of a container."
date = "2015-06-30"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm update"]
weight = 70
+++

# Updating a component

The `swarm update` command enables you to update the container version that's running in a specific component. This results in the component being stopped, the new image being pulled from the registry and then the component being restarted. If your component is scaled to at least several instances, `swarm update` performs a rolling update. According to that, we offer three different rolling update strategies which ideally gives you zero downtime deployment out of the box.

These rolling update strategies are:

* `one-by-one`(default): Updates all instances of your component one after the other.
* `all-at-once`: Updates all instances of your component at the same time.
* `hot-swap`: Replaces all instances of your component by new ones with the desired version with zero downtime. CAUTION: If your component uses volumes, these volumes will be re-created in the process and data from existing volumes will be lost.

If a component was not running before the `swarm update` command is issued on it, it will be started as a result.

You can use the `swarm update` command to either update a component to use a specific version (tag) of an image, or simply let it pull the newest image with he `latest` tag.

## Updating to the latest image version {#latestversion}

Say your full image name is `registry.giantswarm.io/yourusername/yourimage`, make sure that you push the image to the registry with the `:latest` verision tag Here is the `docker push` command for that:

```nohighlight
$ docker push registry.giantswarm.io/yourusername/yourimage:latest
```

(On Linux, you might need to Prepend `sudo` whenever calling the `docker` CLI.)

When the new image is uploaded to the registry, you can issue the `swarm update` command to the latest version using this syntax:

```nohighlight
$ swarm update <app>/<service>/<component>
```

If you were to use the image from the example above inside a component `yourapp/yourservice/yourcomponent`, the comand would be:

```nohighlight
$ swarm update yourapp/yourservice/yourcomponent
```

<!-- TODO: Explain what the expected output would be. WOuld the comand wait until the component is restarted successfully?-->

## Updating to a specific image version {#specificversion}

I you want to make sure the component uses a specific version of your image, you would first tag your image using a specific version number and then upload it to the registry. For example:


```nohighlight
$ docker push registry.giantswarm.io/yourusername/yourimage:2.7.3
```

Then you can pass the version number as a positional argument to the swarm update command.

```nohighlight
$ swarm update <app>/<service>/<component> <version_tag>
```

For example, to update `yourapp/yourservice/yourcomponent` to use version `2.7.3`, you would use this command:

```nohighlight
$ swarm update yourapp/yourservice/yourcomponent 2.7.3
```

## Updating a component using a specific strategy {#specificversion}

Based on the example shown above, you can also specify the type of rolling update strategy you want to use
when updating your component. To do so, you would add the flag `-s|--strategy` followed by the selected strategy.

```nohighlight
$ swarm update -s hot-swap yourapp/yourservice/yourcomponent 2.7.3
$ swarm update -s one-by-one yourapp/yourservice/yourcomponent 2.7.3
$ swarm update -s all-at-once yourapp/yourservice/yourcomponent 2.7.3
```


### Further reading

* [Using the registry](/reference/registry/)
* [Application configuration](/reference/swarm-json/)
