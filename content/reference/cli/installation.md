+++
title = "Installing the Giant Swarm CLI"
description = "A detailed description of how to install Giant Swarm client software on various platforms"
date = "2015-07-03"
type = "page"
weight = 10
+++

# Installing the Giant Swarm CLI

<p class="lead">Instructions for installing the <code>swarm</code> command line interface on the supported platforms. By the way, the latest version is <strong>{{% cli_latest_version %}}</strong>.</p>

## Mac OS X {#macosx}

### Mac OS X install via Homebrew {#macosx-homebrew}

If you're on a Mac, the recommended way to install the `swarm` command line interface (CLI) is to use [Homebrew](http://brew.sh/).

Once Homebrew is set up, these commands will install the latest swarm <abbr title="command line interface">CLI</abbr> in your PATH.

```nohighlight
$ brew tap giantswarm/swarm
$ brew install swarm-client
```

In order to update the swarm <abbr title="command line interface">CLI</abbr> to the latest version, use these commands:

```nohighlight
$ brew update
$ brew upgrade swarm-client
```

### Mac OS X manual install {#macosx-manual}

If you don't want to use Homebrew, here is how you could download and install the <abbr title="command line interface">CLI</abbr> manually.

```nohighlight
$ curl -O http://downloads.giantswarm.io/swarm/clients/{{% cli_latest_version %}}/swarm-{{% cli_latest_version %}}-darwin-amd64.tar.gz
$ tar xzf swarm-{{% cli_latest_version %}}-darwin-amd64.tar.gz
```

To check the integrity of the downloaded .tar.gz file, we provide [SHA1](http://downloads.giantswarm.io/swarm/clients/{{% cli_latest_version %}}/swarm-{{% cli_latest_version %}}-darwin-amd64.tar.gz.sha1) and [MD5](http://downloads.giantswarm.io/swarm/clients/{{% cli_latest_version %}}/swarm-{{% cli_latest_version %}}-darwin-amd64.tar.gz.md5) checksum files.

Please add the `swarm` binary to your PATH.

## Linux {#linux}

Please note that the `swarm` <abbr title="command line interface">CLI</abbr> requires a 64 bit system.

First, download the tarball and unpack it:

```nohighlight
$ curl -O http://downloads.giantswarm.io/swarm/clients/{{% cli_latest_version %}}/swarm-{{% cli_latest_version %}}-linux-amd64.tar.gz
$ tar xzf swarm-{{% cli_latest_version %}}-linux-amd64.tar.gz
```

To check the integrity of the downloaded .tar.gz file, we provide [SHA1](http://downloads.giantswarm.io/swarm/clients/{{% cli_latest_version %}}/swarm-{{% cli_latest_version %}}-linux-amd64.tar.gz.sha1) and [MD5](http://downloads.giantswarm.io/swarm/clients/{{% cli_latest_version %}}/swarm-{{% cli_latest_version %}}-linux-amd64.tar.gz.md5) checksum files.

We recommend to make the `swarm` binary available in your PATH by copying it to a directory that's already contained in your PATH. For example:

```nohighlight
$ sudo cp swarm /usr/local/bin/
```

## Windows {#windows}

Currently, we don't offer the swarm CLI for Windows. However, you can run it in a Linux virtual machine like the one used by [Boot2Docker](https://docs.docker.com/installation/windows/). Simply log in to Boot2Docker's virtual machine via `boot2docker ssh` and follow the installation instructions for Linux ([see above](#linux)).

## Using an HTTP proxy

The swarm client you install is programmed in [Go](http://golang.org/). That way we can leverage several build in features. For example you can configure a proxy for the connection between the CLI and the swarm cluster by simply setting the HTTP_PROXY env variable.

In Bash this would be:
```nohighlight
export HTTP_PROXY="http://proxyIp:proxyPort"
```


## Next steps

* [The Annotated Hello World Example](/guides/annotated-helloworld/): A quick check that everything is working fine
* [Your First Application - in Your Language](/guides/your-first-application/): Learn how to create your first application on Giant Swarm on your prefered technology
