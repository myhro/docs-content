+++
title = "swarm login"
description = "Reference page for the `swarm login` command, which allows you to authenticate with your Giant Swarm credentials."
date = "2015-03-19"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm login"]
weight = 40
+++

# `swarm login`: Login with the CLI

The `swarm login` command allows you to authenticate to your Giant Swarm account using the <abbr title="command line interface">CLI</appr>.

The command can be used in interactive mode or credentials can be passed via the command line in non-interactive mode.

## Interactive Login

You can invoke an interactive login session by doing the following:

```nohighlight
swarm login
```

Alternately, you can pass in your username by doing the following while substituting the `<username>`:

```nohighlight
swarm login <username>
```

#### Example with Response

```nohighlight
$ swarm login
Username or email: bant
Password:
Login Succeeded
Environment bant/dev has been selected
```

*Note: The last line of output informs you which environment has been selected. The default is `<yourusername>/dev` which can be changed using the [`swarm env`](/reference/cli/env/) command.*

## Non-Interactive Login
In non-interactive mode, the username and password can be passed to the login command as arguments:

```nohighlight
swarm login <username> -p <password>
```

#### Example with Response

```nohighlight
$ swarm login bant -p f00bar
Login Succeeded
Environment bant/dev has been selected
```

## Authentication Tokens

When logging in using the `swarm login` command, authentication tokens are created and stored locally in `~/.swarm/token`:

```nohighlight
$ ls -la ~/.swarm/
total 24
drwxr-xr-x   5 bant  staff   170 May 18 23:40 .
drwxr-xr-x+ 53 bant  staff  1802 May 28 11:11 ..
-rw-------   1 bant  staff    34 May 18 23:40 config
-rw-------   1 bant  staff    39 May 27 11:24 last_update_info
-rw-------   1 bant  staff    36 May 27 20:30 token
```

Giant Swarm authentication tokens are good for up to 30 days. To minimize the risk of unauthorized use, please use the [`swarm logout`](/reference/cli/logout/) command to explicitly expire the authenticated token and session.

The [API documentation](/reference/api/#auth) contains more information about acquiring and using authentication tokens.

## Further reading

* [Environments](/reference/cli/env/)
* [Logging Out](/reference/cli/logout/)
* [API Documentation](/reference/api/)
