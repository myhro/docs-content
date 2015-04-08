+++
title = "Collaborating as a team"
description = "This guide gives an introduction on how to collaborate as a team when bringing applications to Giant Swarm."
date = "2015-03-06"
type = "page"
weight = 10
categories = ["advanced"]
+++

# Collaborating as a team

<p class="lead">Giant Swarm provides the means to collaborate on applications as a team. This guide gives an introduction to the most important aspects to take care of when deploying and controlling applications in a multi-user work group.</p>

## Organizations and environments

An [organization](/reference/org/) on Giant Swarm is a simply a named group of users. An [environment](/reference/env/) is a named context in which to deploy applications. Each environment belongs to an organization. This is visible in the environment name, which is always prefixed with an organization name: `<organization_name>/<environment_suffix>`.

Whenever you're logged in with the `swarm` CLI and interact with our infrastructure, an environment is selected as your context. This even happens without you knowing about these concepts. With your first login, an organization named after your username is created and an environment with the name pattern `<organization_name>/dev` is created and selected. This is called the default environment.

However, for collaboration in a team, there should be a dedicated organization for any team you work with. You can create as many organizations as you like, and each organization can have as many environments as needed.

## Creating an organization for your team

The basis for team collaboration is to have an organization that every team member belongs to. So the first step when starting to work as a team is to create the appropriate organization using the `swarm CLI`.

Probably the hardest part here is to come up with a good organization name. Organization names are between 4 and 30 characters long and only consist of the characters a-z, 0-9 and the underscore (_). Also, the name has to be unique for the entire Giant Swarm platform.

Say you want to create a new organization called `ateam`, this is how you would do it:

```nohighlight
$ swarm org create ateam
```

As the creator of the organization, you are automatically a member. Find proof for that by listing all members of the newly created organization:

```nohighlight
$ swarm org show ateam
Organization ateam

Username:
hannibal
```

## Adding team members to the organization

To add a user to an organization, you need to know the Giant Swarm username of the user to add. To add a user called `murdoch` to the organization `ateam`, use this command:

```nohighlight
$ swarm org add-user ateam murdock
```

## Naming images for team access

The building blocks of your Giant Swarm applications are Docker containers, which can be created from images on any public registry or the [private registry](/reference/registry/) Giant Swarm provides. This section assumes that you want to make use of our private registry.

When working only on your own, you can simply tag images for your application components using your username as a namespace identifer. The schema is:

```nohighlight
registry.giantswarm.io/<yourusername>/<imagename>:<tag>
```

When your applications are supposed to belong to a team, all team members should be able to push new image versions to the registry. This can be accomplished by using the organization name (instead of a username) as namespace identifier. The image schema then has to be:

```nohighlight
registry.giantswarm.io/<organization_name>/<image_name>:<tag>
```

You can set the appropriate image name either immediately when creating an image using `docker build`, or in a seperate step after creating the image, using `docker tag`.

For our organization called `ateam`, an image with the name `myimage` and some tag could look like this:

```nohighlight
registry.giantswarm.io/ateam/myimage:latest
```

To push this image to the registry, use the `docker push` command with the entire image name, including tag, as argument:

```nohighlight
$ docker push registry.giantswarm.io/ateam/myimage:latest
```

## Deploying applications for team access

Now that it's set that the team has access to our images, we can deploy the according application.

As said in the introduction, whenever you work on the Giant Swarm infrastructure, you act in a distinct environment. To make your application accessible to your team (organization), the environment running the application has to be owned by the according organization.

When you just created a new organization as explained above, you will now have to cerate a new environment belonging to that organization.

### Creating an environment and selecting it

Again, you'll have to come up with a name for the environment. It only has to be unique within the context of the organization, so it can be nice and short. Common environment names tell something about their purpose, e. g. `<somecompena>/dev` or `<somecompena>/production`.

Creating a new environment `ateam/dev`, for example, could be done using this command:

```
$ swarm env ateam/dev
```

Not only does this create the environment, it also selects it as the current one. You can verify this using either the `swarm env` command (without argument) or the `swarm info` command (here noting the "Current environment").

So, regardless if the environment existed before or not, `swarm env ateam/dev` will select the according environment for the current user. Every team member has to do this in order to conveniently control applications running in this environment.

As you might have guessed, only members of the organization `ateam` are allowed to select its environments.

### Deploying applications

Once the team environment is selected, all deployments (using `swarm up` or `swarm create`) will automatically happen within this environment, unless specified otherwise.

Your [application configuration](/reference/swarm-json/) has to make use of the appropriate image namespace identifiers, as explained above in [tagging images for team access](#tagging-images-for-team-access). This means: If your current environment is `ateam/dev`, your image names should start with `registry.giantswarm.io/ateam/`.

## Wrapping it up

This is all there is to it. You have just learned how to create an organization for your team and add members to it, creating environments for the team, and tagging images to be accessed by the team.

## Further reading

* [Reference: Managing organizations](/reference/org/)
* [Reference: Managing Environments](/reference/env/)
* [Reference: Using the Registry](/reference/registry/)
* [Reference: Application configuraiton (swarm.json)](/reference/swarm-json/)
