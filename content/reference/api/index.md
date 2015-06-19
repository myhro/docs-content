+++
title = "Giant Swarm API Reference"
description = "The Giant Swarm API allows you to create, control and monitor applications programmatically. The reference contains descriptions of all building blocks."
date = "2015-06-01"
type = "page"
categories = ["Reference", "API"]
tags = ["api"]
weight = 100
+++

# The Giant Swarm API V1

The Giant Swarm API is a simple, mostly RESTful JSON based API, which can be accessed over an encrypted HTTPS connection. The API is used by the Giant Swarm `swarm` CLI command and should be incorporated into your software where programmatic control over your infrastructure is required.

*Note: The Giant Swarm API and this documentation is still in alpha and should be expected to change over time.*

## The Basics {#basics}

The following information is intended to familiarize you with the use of the Giant Swarm API. If you have a suggestion on how to improve the documentation or have found an error, [please open a ticket](https://github.com/giantswarm/docs-content/issues).

There are several types of management methods supported by the Giant Swarm API:

1. [Organization](#organization)
1. [Application](#app)
1. [Service](#service)
1. [Component](#component)
1. [Instance](#instance)
1. [Connection](#connection)
1. [User](#user)

### Introduction Video

The following video gives a quick overview of the Giant Swarm API using simple curl commands provided in this guide.

<div class="embed-responsive embed-responsive-16by9" style="padding-bottom: 56.5%">
  <iframe class="embed-responsive-item" width="560" height="315" src="https://player.vimeo.com/video/128089952" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

### Changelog

Follow the [API changelog](/reference/api/changelog/) for information on new functionality as well as changes and bug fixes to existing methods.

### Example Helper

For convenience, this page contains an <a id="link-helper" href="#helper">example helper</a> designed to replace variables used in examples on this page with ones that are valid for your account. The helper can be triggered by clicking on the link above, or hitting the `h` key while on this page. Please note the helper will not work when viewing this documentation on Github.

You may jump to the [password authentication](#passwordauth) section for information on retrieving an authentication token for use with the example helper.

### Naming Conventions

This document uses brackets `< >` to identify variables such as organization and application names where they appear in JSON based requests. The remainder of the document uses braces `{ }` to identify variables where they appear outside JSON requests, such as in paths and request headers.

### Resource URLs

All Giant Swarm API resources live under the following URL:

```nohighlight
https://api.giantswarm.io/
```

The API is currently in Version 1.0, indicated by the `/v1/` path:

```nohighlight
https://api.giantswarm.io/v1/
```

The `/org/`, `/env/` and `/app/` path parameters denote the concepts of organizations, environments and applications within the Giant Swarm service. These are respectively combined with their named objects within the system.

Here is an example URL which refers to information about the organization named `bantic`:

```nohighlight
https://api.giantswarm.io/v1/org/bantic/
```

### SDKs

No public SDKs exist for the Giant Swarm API at this time, but we plan on adding them to this section when they become available. Please feel free to [comment on Discourse](http://discourse.giantswarm.io/t/sdks-for-the-giant-swarm-api/32) if you are interested in developing an SDK in your favorite language!

### Response Codes

Giant Swarm's API returns the standard [HTTP status codes](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). The API also uses a set of internal response codes which are returned with each request:

| Response Code | Response Type |
|-----|------|
| 10000 | STATUS_CODE_DATA |
| 10001 | STATUS_CODE_RESOURCE_UP |
| 10002 | STATUS_CODE_RESOURCE_DOWN |
| 10003 | STATUS_CODE_RESOURCE_CREATED |
| 10004 | STATUS_CODE_RESOURCE_STARTED |
| 10005 | STATUS_CODE_RESOURCE_STOPPED |
| 10006 | STATUS_CODE_RESOURCE_UPDATED |
| 10007 | STATUS_CODE_RESOURCE_DELETED |
| 10008 | STATUS_CODE_RESOURCE_NOT_FOUND |
| 10009 | STATUS_CODE_RESOURCE_ALREADY_EXISTS |
| 10010 | STATUS_CODE_RESOURCE_INVALID_CREDENTIALS |
| 10011 | STATUS_CODE_CONDITION_TRUE |
| 10012 | STATUS_CODE_CONDITION_FALSE |
| 10013 | STATUS_CODE_WRONG_INPUT |
| 10014 | STATUS_CODE_USER_ERROR |
| 10015 | STATUS_CODE_SERVER_ERROR |
| 10016 | STATUS_CODE_INVALID_VERSION_ERROR |

### Viewing API Methods via the CLI

The [Giant Swarm CLI](/reference/cli/installation/) can be used with the `--debug` flag to show the methods it uses when it talks to the API.

Here is an example use of the CLI which lists all applications in the `bantic` organization's `dev` environment and then queries each one of them for its status:

```nohighlight
$ swarm --debug ls
DEBUG: GET https://api.giantswarm.io/v1/org/bantic/env/dev/app/
DEBUG: << 200 OK
DEBUG: GET https://api.giantswarm.io/v1/org/bantic/env/dev/app/currentweather/status
DEBUG: << 200 OK
1 application available in environment 'terminal/dev':

application     created              status
currentweather  2015-04-28 17:28:36  up
```

In this example, we can see we should use a `GET` method on the `/v1/org/bantic/env/dev/app/` endpoint to get a list of applications in our `dev` environment.

### Processing Responses with bash
You may occasionally wish to prototype API calls in `bash`. Here's an example which uses the `jq` command to extract the required data from the JSON response data:

```nohighlight
curl -sS \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/ \
    | jq '.data.environments[0].name'

"dev"
```

*Note: If you are running on OSX, you may install the `jq` command by doing a `brew install jq`. You can find more information about `jq`, including install information for Linux, [on its website](http://stedolan.github.io/jq/)*.

## Authentication {#auth}

The Giant Swarm API requires authentication tokens for most of its URL endpoints. Tokens may be retrieved from the command line if the `swarm` client is [installed](/reference/cli/installation/) and [authenticated](/reference/cli/login/):

```nohighlight
$ cat ~/.swarm/token; echo;
e5239484-2299-41df-b901-d0568db7e3f9
```

Authentication tokens may also be obtained by using the `/v1/user/{username}/login` endpoint while sending an encoded password via an `application/json` content type inside the `POST` request.

### Password Authentication

To authenticate and get a token, call the `POST` method on the `/v1/user/{username}/login` method using a JSON object containing a `base64` encoded password. :

```nohighlight
curl -sS \
    -H "Content-Type: application/json" \
    -X POST \
    -d '{"password":"<base64_password>"}' \
    https://api.giantswarm.io/v1/user/{username}/login
```

Here's an example which uses a bash partial using `echo` with the `-n` (no line feed) option to populate the password dynamically and uses Python to clean up the output:

```nohighlight
curl -sS \
    -H "Content-Type: application/json" \
    -X POST \
    --data '{"password":"'"$(echo -n f00bar | base64)"'"}' \
    https://api.giantswarm.io/v1/user/terminal/login \
    | python -mjson.tool

{
    "data": {
        "Id": "e5239484-2299-41df-b901-d0568db7e3f9",
    },
    "status_code": 10000,
    "status_text": "success"
}
```

*Note: Be careful when using the `base64` command to generate encoded data from STDIN. For example, the default behavior of `echo foo | base64` in bash will result in a line feed `'\n'` being encoded in the password. Giant Swarm's API will not authorize a password with an appended line feed or carriage return.*

### Token Authentication

Tokens for use with API calls may be obtained by requesting the `/v1/user/{username}/login` endpoint, as shown above. The token is identified by the `Id` key in the response. In the example above, the token would be:

```text
e5239484-2299-41df-b901-d0568db7e3f9
```

This token must be passed in the headers of the API request. In this example `curl` request, we set the auth request header to `Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9`. Please note the string `giantswarm` is always required, and should not be substituted with another string.

Here's an example of using a token to `curl` the `/v1/org/bantic/env/` endpoint to list available environments:

```nohighlight
curl -sS \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/ \
    | python -mjson.tool

{
  "data": {
    "environments": [
      {
        "name": "dev"
      }
    ]
  },
  "status_code": 10000,
  "status_text": "success"
}
```

## Organization Methods {#organization}

The organization methods live under the `/v1/org/` endpoint. The following table lists the available methods for operating on organizations:

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/env/ | [GET](#ListEnvironments) | Get the environments within the given organization that contain applications. |
| /v1/org/ | [POST](#CreateOrganization) | Create a new organization. |
| /v1/org/\{org\}/ | [DELETE](#DeleteOrganization) | Remove an organization. |
| /v1/org/\{org\} | [GET](#GetOrganization) | Get information about an organization. |
| /v1/org/\{org\}/members/add | [POST](#AddOrganizationMember) | Add a member to an organization. |
| /v1/org/\{org\}/members/remove | [POST](#RemoveOrganizationMember) | Remove a member from an organization. |

In addition to the path parameters shown above, the following JSON POST parameters are used by the organization methods:

| Key | Value Type | Description | Required by related methods? |
|-----|-----|-----|-----|
| org_id | string | The new organization's name. | Yes |
| username | string | The username for the new user. | Yes |

### Listing an Organization's Environments {#ListEnvironments}

To request a list of an organization's environments which contain appellations, call the `GET` method on the `/v1/org/{org}/env/` endpoint:

```nohighlight
curl -sS \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/ \
    | python -mjson.tool

{
  "data": {
    "environments": [
      {
        "name": "dev"
      },
      {
        "name": "prod"
      }
    ]
  },
  "status_code": 10000,
  "status_text": "success"
}
```
*Note: The use of the `| python -mjson.tool` is optional, and used for formatting purposes only.*

### Create a New Organization {#CreateOrganization}

To create a new organization in an account, call the `POST` method on the `/v1/org/` endpoint with JSON data containing the new org's name:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    --data '{"org_id":"<org>"}' \
    https://api.giantswarm.io/v1/org/
```
*Note: The default organization name will initially be the username of the account.*

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    --data '{"org_id":"bantic"}' \
    https://api.giantswarm.io/v1/org/ \
    | python -mjson.tool

{
  "status_code": 10003,
  "status_text": "resource created"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10003 | The organization was created. |
| 400 | 10015 | An organization with the same name already exists. |

### Remove an Organization {#DeleteOrganization}

To remove an existing organization, call the `DELETE` method on the `/v1/org/{org}/` endpoint:

```nohighlight
curl -sS \
    -X DELETE \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/
```

*Note: You must use a trailing slash on the end of the URL when doing a `DELETE` on an organization.*

#### Example with JSON Response

```nohighlight
curl -sS \
    -X DELETE \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/ \
    | python -mjson.tool

{
  "status_code": 10007,
  "status_text": "resource deleted"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10007 | The organization was removed. |
| 403 | n/a | The organization does not exist or removal is forbidden. |

### Get Information on Organization {#GetOrganization}

To get information on an existing organization, call the `GET` method on the `/v1/org/{org}/` endpoint:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}
```

*Note: You may NOT use a trailing slash on the end of the URL when doing a `GET` on an object.*

#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic \
    | python -mjson.tool

{
  "data": {
    "default_cluster": "alpha.private.giantswarm.io",
    "id": "bantic",
    "members": [
      "terminal"
    ]
  },
  "status_code": 10000,
  "status_text": "success"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The organization data was returned. |
| 404 | n/a | The organization was not found. |

### Add a Member to an Organization {#AddOrganizationMember}

To add a new user/member to an existing organization, call the `POST` method on the `/v1/org/{org}/members/add` endpoint with JSON data containing the new member's username:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    --data '{"username":"<username>"}' \
    https://api.giantswarm.io/v1/org/{org}/members/add
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    --data '{"username":"terminal"}' \
    https://api.giantswarm.io/v1/org/bantic/members/add \
    | python -mjson.tool

{
  "status_code": 10006,
  "status_text": "resource updated"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10006 | The user was added. |
| 400 | 10013 | The username was not found or has already been added. |
| 403 | n/a | The organization was not found or update is forbidden. |

### Remove a Member from an Organization {#RemoveOrganizationMember}

To add a remove a user/member from an existing organization, call the `POST` method on the `/v1/org/{org}/members/remove` endpoint with JSON data containing the member's username:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    --data '{"username":"<username>"}' \
    https://api.giantswarm.io/v1/org/{org}/members/remove
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    --data '{"username":"terminal"}' \
    https://api.giantswarm.io/v1/org/bantic/members/remove \
    | python -mjson.tool

{
  "status_code": 10006,
  "status_text": "resource updated"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10006 | The resource was updated and the member removed. |
| 400 | 10013 | A member with that username was not found or has already been removed. |
| 403 | n/a | The organization was not found or update is forbidden. |


## Application Methods {#app}

The operations methods for applications live under the `/v1/org/{org}/env/{env}/app/` endpoint. The following table lists the available methods for operating on applications:

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/env/\{env\}/app/ | [GET](#ListApps) | Return a list of all applications running in a specific environment. |
| /v1/org/\{org\}/env/\{env\}/app/ | [POST](#CreateApp) | Create a new application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\} | [DELETE](#DeleteApp) | Delete an existing application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/start | [POST](#StartApp) | Start an existing application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/stop | [POST](#StopApp) | Stop an existing application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/status | [GET](#StatusApp) | Query the status of an application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/config | [GET](#ConfigApp) | Get the configuration of an application. |

In addition to the path parameters shown above, the following parameters are used inside the JSON `POST`:

| Key | Value Type | Description | Required by related methods? |
|-----|-----|-----|-----|
| app_name | string | The new application's name. | Yes |
| args | array | A list of arguments passed to the entry point. | No |
| components | array | A list of the service's components. | Yes |
| component_name | string | Name of the component | Yes |
| dependencies | array | List of dependencies of this component | No |
| domains | dict | A dictionary of domain keys to port numbers. | Yes |
| env | array | List of environment variables used by a component. | No |
| image | string | The name or URI for an image. | Yes |
| ports | array | A list of strings containing port and protocol pairs. | Yes |
| scaling_policy | dict | Scaling settings for a component. | No |
| services | array | A list of the application's services. | Yes |
| service_name | string | The name for each service. | Yes |
| volumes | array | List of volumes to attach to this component. | No |

You may refer to the [application configuration reference](/reference/swarm-json/) for more information about the ```swarm.json``` file format.

### List Running Applications {#ListApps}

To list running applications for a given organization and environment, call the `GET` method on the `/v1/org/{org}/env/{env}/app/` endpoint:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/ \
    | python -mjson.tool

{
  "data": [
    {
      "app": "ghost-blog",
      "company": "bantic",
      "created": "2015-05-07 03:28:18",
      "env": "dev",
      "org": "bantic"
    },
    {
      "app": "helloworld",
      "company": "bantic",
      "created": "2015-05-11 19:51:37",
      "env": "dev",
      "org": "bantic"
    }
  ],
  "status_code": 10000,
  "status_text": "success"
}
```

*Note: The `company` and `org` object types are equivalent and `company` will be deprecated in future releases of the Giant Swarm API.*

#### Response Status Codes
| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The resource data was returned. |
| 400 | 10008 | The organization or environment could not be found. |
| 403 | n/a | Access to the resource was denied. |

### Create a New Application {#CreateApp}

To create a new application for a given organization and environment, call the `POST` method on the `/v1/org/{org}/env/{env}/app/` endpoint using a JSON formatted file:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    --data @{json_file} \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/
```

*Note: Once an application has been created, you will need to start it with the `start` API method.*

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    --data @swarm.json \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/ \
    | python -mjson.tool

{
  "status_code": 10003,
  "status_text": "resource created"
}
```

*Note: If you run this example, you will need to checkout the repo:*

`git clone https://github.com/giantswarm/python-flask-helloworld.git`

#### Example JSON File

This JSON should be saved as `swarm.json` for the above example to work:

```json
{
  "app_name": "helloworld",
  "services": [
    {
      "service_name": "webserver",
      "components": [
        {
          "component_name": "flask",
          "image": "registry.giantswarm.io/bantic/helloworld",
          "ports": [5000],
          "domains": {
            "apidocsdemo.gigantic.io": 5000
          }
        }
      ]
    }
  ]
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10003 | The application was created. |
| 400 | 10009 | The application already exists. |
| 403 | n/a | Access to the resource is forbidden. |

### Delete an Application {#DeleteApp}

To delete an existing application for a given organization and environment, call the `DELETE` method on the `/v1/org/{org}/env/{env}/app/{app}` endpoint:

```nohighlight
curl -sS \
    -X DELETE \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X DELETE \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld \
    | python -mjson.tool

{
  "status_code": 10007,
  "status_text": "resource deleted"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10007 | The application was deleted. |
| 403 | n/a | Access to the resource is forbidden. |
| 404 | n/a | The application was not found. |

### Start an Application {#StartApp}

To start an existing application for a given organization and environment, call the `POST` method on the `/v1/org/{org}/env/{env}/app/{app}/start` endpoint:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/start
```
#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/start \
    | python -mjson.tool

{
  "status_code": 10004,
  "status_text": "resource started"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10004 | The application was started. |
| 400 | 10008 | The application was not found. |
| 403 | n/a | Access to the resource is forbidden. |

### Stop an Application {#StopApp}

To stop an existing application for a given organization and environment, call the `POST` method on the `/v1/org/{org}/env/{env}/app/{app}/stop` endpoint:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/stop
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/stop \
    | python -mjson.tool

{
  "status_code": 10005,
  "status_text": "resource stopped"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10005 | The application was stopped. |
| 400 | 10008 | The application was not found. |
| 403 | n/a | Access to the resource is forbidden. |

### Get an Application's Status {#StatusApp}

To get an existing application's status for a given organization and environment, call the `GET` method on the `/v1/org/{org}/env/{env}/app/{app}/status` endpoint:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/status
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/status \
    | python -mjson.tool

{
  "data": {
    "name": "helloworld",
    "services": [
      {
        "components": [
          {
            "instances": [
              {
                "create_date": "2015-05-16T01:01:20Z",
                "id": "by3we1wr77b7",
                "image": "registry.giantswarm.io/bantic/helloworld",
                "image_hash": "",
                "status": "down"
              }
            ],
            "max": 10,
            "min": 1,
            "name": "flask",
            "status": "down"
          }
        ],
        "max": 10,
        "min": 1,
        "name": "webserver",
        "status": "down"
      }
    ],
    "status": "down"
  },
  "status_code": 10000,
  "status_text": "success"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The resource data was returned. |
| 400 | 10008 | The application was not found. |
| 403 | n/a | Access to the resource is forbidden. |

### Get an Application's Configuration {#ConfigApp}

To get an existing application's configuration for a given organization and environment, call the `GET` method on the `/v1/org/{org}/env/{env}/app/{app}/config` endpoint:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/config
```
#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/config \
    | python -mjson.tool

{
  "data": {
    "app_name": "helloworld",
    "services": [
      {
        "components": [
          {
            "component_name": "flask",
            "domains": {
              "apidocsdemo.gigantic.io": 5000
            },
            "image": "registry.giantswarm.io/bantic/helloworld",
            "ports": [
              5000
            ]
          }
        ],
        "service_name": "webserver"
      }
    ]
  },
  "status_code": 10000,
  "status_text": "success"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The resource data was returned. |
| 400 | 10008 | The application was not found. |
| 403 | n/a | Access to the resource is forbidden. |

## Service Methods {#service}

The service methods live under the `/v1/org/{org}/env/{env}/app/{app}/service/` endpoint. The following table lists the available methods for operating on services:

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/start | [POST](#StartService) | Start a service. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/stop | [POST](#StopService) | Stop a service. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/status | [GET](#StatusService) | Get the status of an existing service. |

### Starting a Service {#StartService}

To start a service for a given application, call the `POST` method on the `/v1/org/{org}/env/{env}/app/{app}/service/{service}/start` endpoint:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/service/{service}/start
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/service/webserver/start \
    | python -mjson.tool

{
  "status_code": 10004,
  "status_text": "resource started"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10004 | The service was started. |
| 400 | 10008 | The service was not found. |
| 403 | n/a | Access to the resource is forbidden. |

### Stop a Service {#StopService}

To stop a service for a given application, call the `POST` method on the `/v1/org/{org}/env/{env}/app/{app}/service/{service}/stop` endpoint:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/service/{service}/stop
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/service/webserver/stop \
    | python -mjson.tool

{
  "status_code": 10004,
  "status_text": "resource stopped"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10004 | The service was stopped. |
| 400 | 10008 | The service was not found. |
| 403 | n/a | Access to the resource is forbidden. |

### Get a Service's Status {#StatusService}

To get a service's status for a given application, call the `GET` method on the `/v1/org/{org}/env/{env}/app/{app}/service/{service}/status` endpoint:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/service/{service}/status
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/service/webserver/status \
    | python -mjson.tool
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The resource data was returned. |
| 400 | 10008 | The service was not found. |
| 403 | n/a | Access to the resource is forbidden. |

## Component Methods {#component}

The component methods live under the `/v1/org/{org}/env/{env}/app/{app}/service/{service}/component/{component}` endpoint. The following table lists the available and very long methods for operating on a service component:

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/<br/>component/\{component\}/status | [GET](#StatusComponent) | Query the status of an existing component. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/<br/>component/\{component\}/version/\{version\}/update | [POST](#UpdateComponent) | Update the docker image version of an existing component. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/<br/>component/\{component\}/scaleup/\{scale\} | [POST](#ScaleUpComponent) | Add instances of an existing component. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/<br/>component/\{component\}/scaledown/\{scale\} | [POST](#ScaleDownComponent) | Remove instances of an existing component. |

### Query the Status of an Existing Component {#StatusComponent}

To get the status a given component for an application's service, call the `GET` method on the `/v1/org/{org}/env/{env}/app/{app}/service/{service}/component/{component}/status` endpoint:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/service/{service}/component/{component}/status
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/service/webserver/component/flask/status \
    | python -mjson.tool

{
  "data": {
    "components": [
      {
        "instances": [
          {
            "create_date": "2015-05-16T01:01:20Z",
            "id": "by3we1wr77b7",
            "image": "registry.giantswarm.io/startup/helloworld",
            "image_hash": "9fa830d596f3ba5841ca3d632db4132a53699358e1ad9853d9b9c84e080af27b",
            "status": "up"
          }
        ],
        "max": 10,
        "min": 1,
        "name": "flask",
        "status": "up"
      }
    ],
    "max": 10,
    "min": 1,
    "name": "webserver",
    "status": "up"
  },
  "status_code": 10000,
  "status_text": "success"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The resource data was returned. |
| 400 | 10008 | The service was not found. |
| 403 | n/a | Access to the resource is forbidden. |

### Update an Existing Component {#UpdateComponent}

To update a service component's image for an application, call the `POST` method on the `/v1/org/{org}/env/{env}/app/{app}/service/{service}/version/{version}/update` endpoint.
You can optionally choose among three different rolling update strategies that will
change the way this update operation is triggered over your component.

These strategies are:

* `one-by-one`(default) updates all the instances of your component one after the other.
* `all-at-once` updates all the instances of your component at once.
* `hot-swap` replaces all current instances by new ones with the desired version with zero downtime.

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    --data '{"strategy":"one-by-one|all-at-once|hot-swap"}' \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/service/{service}/component/{component}/version/{version}/update
```

*Note: Updating a component's image is limited to changing the tagged version of the image. You may not change the image name with an update.*

#### Example with JSON Response
This example updates the current service component to use the [3.3.6 version of the Python image](https://registry.hub.docker.com/_/python/) in the Docker Index:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/service/webserver/component/flask/version/3.3.6/update \
    | python -mjson.tool

{
  "status_code": 10006,
  "status_text": "resource updated"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10006 | The component was updated. |
| 400 | 10008 | The service was not found. |
| 403 | n/a | Access to the resource is forbidden. |

### Add an Instance to a Component {#ScaleUpComponent}

To add instances to a service component, call the `POST` method on the `/v1/org/{org}/env/{env}/app/{app}/service/{service}/component/{component}/scaleup/{scale}` endpoint.  Use the `{scale}` path parameter as the number of additional instances to start for the service component:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}/service/{service}/component/{component}/scaleup/{scale}
```
*Note: While in testing, Giant Swarm accounts support up to 10 instances per component.*

#### Example with JSON Response

This example starts an additional `3` instances for the service component for the `helloworld` application:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld\
    /service/webserver/component/flask/scaleup/3 \
    | python -mjson.tool

{
  "status_code": 10006,
  "status_text": "resource updated"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10006 | The component was updated. |
| 400 | 10008 | The component was not found. |
| 403 | n/a | Access to the resource is forbidden. |

### Remove Instances from a Component {#ScaleDownComponent}

To remove instances from a service component, call the `POST` method on the `/v1/org/{org}/env/{env}/app/{app}/service/{service}/component/{component}/scaledown/{scale}` endpoint. Use the `{scale}` path parameter as the number of instances to remove from the service component:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/env/{env}/app/{app}\
    /service/{service}/component/{component}/scaledown/{scale}
```
*Note: While in testing, Giant Swarm accounts support up to 10 instances per component.*

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld\
    /service/webserver/component/flask/scaledown/2 \
    | python -mjson.tool

{
  "status_code": 10006,
  "status_text": "resource updated"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10006 | The component was updated. |
| 400 | 10008 | The component was not found. |
| 403 | n/a | Access to the resource is forbidden. |

## Instance Methods {#instance}

The instance methods live under the /v1/org/{org}/instance/{instance} endpoint. The following table lists the available methods for operating on a component's instances:

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/instance/\{instance\}/logs | [GET](#LogInstance) | Get logs of an instance. |
| /v1/org/\{org\}/instance/\{instance\}/stats | [GET](#StatInstance) | Get statistics of an instance. |
| /v1/org/\{org\}/instance/\{instance\}/exec | [POST](#ExecInstance) | Execute a command inside an instance or opens a shell for remote access. |

*Note: The `{instance}` path parameter should use an instance's ID, which is obtained via the  [component status method](#StatusComponent).*

#### Retrieving an Instance ID with curl and jq
If you have the `jq` tool installed, instance IDs may be returned directly by querying the component status method:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/env/dev/app/helloworld/service/webserver/component/flask/status \
    | jq .data.instances[0].id

"by3we1wr77b7"
```

### Get an Instance's Logs {#LogInstance}

To get an instance's logs, call the `GET` method on the `/v1/org/{org}/instance/{instance}/logs` endpoint.

```nohighlight
    curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/instance/{instance}/logs
```

#### Example with TEXT Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/instance/by3we1wr77b7/logs

2015-05-16 01:01:59.326 +0000 UTC    - docker  - * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
2015-05-16 01:03:03.839 +0000 UTC    - docker  - 172.17.42.1 - - [16/May/2015 01:03:03] "GET / HTTP/1.1" 200 -
2015-05-16 01:03:04.223 +0000 UTC    - docker  - 172.17.42.1 - - [16/May/2015 01:03:04] "GET /favicon.ico HTTP/1.1" 404 -
2015-05-16 01:03:04.393 +0000 UTC    - docker  - 172.17.42.1 - - [16/May/2015 01:03:04] "GET /favicon.ico HTTP/1.1" 404 -
2015-05-16 01:03:12.283 +0000 UTC    - docker  - 172.17.42.1 - - [16/May/2015 01:03:12] "GET /hello/kord HTTP/1.1" 200 -
```
*Note: The response from the `logs` method is TEXT formatted.*

### Get an Instance's Statistics {#StatInstance}

To get an instance's stats, call the `GET` method on the `/v1/org/{org}/instance/{instance}/stats` endpoint.

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    https://api.giantswarm.io/v1/org/{org}/instance/{instance}/stats
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    https://api.giantswarm.io/v1/org/bantic/instance/by3we1wr77b7/stats \
    | python -mjson.tool

{
  "status_code": 10000,
  "status_text": "success",
  "data": {
    "ComponentName": "helloworld/webserver/flask",
    "MemoryUsageMb": 25.21875,
    "MemoryCapacityMb": 512,
    "MemoryUsagePercent": 4.925537109375,
    "CpuUsagePercent": 0.0120874
  }
}
```

### Execute a Remote Command on an Instance {#ExecInstance}

To execute a command on a given instance, call the `POST` method on the `/v1/org/{org}/instance/{instance}/exec` endpoint:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    --data '{"cmd":["<command>"],"detach":true}' \
    https://api.giantswarm.io/v1/org/bantic/instance/{instance}/exec
```
In addition to the path parameters above, the following parameters are used with the `exec` method:

#### Example with TEXT Response:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: text/plain" \
    -H "Connection: Upgrade" \
    -H "Upgrade: tcp" \
    --data '{"cmd":["ps","-ax"],"detach":false,"attach-stdout":true,"attach-stdin":true,"raw":true}' \
    https://api.giantswarm.io/v1/org/bantic/instance/by3we1wr77b7/exec

  PID TTY      STAT   TIME COMMAND
    1 ?        Ss     0:00 sh -c echo "Hello from Giant Swarm. \o/" > index.html
    8 ?        S      0:02 python -m http.server
   92 ?        S+     0:00 /bin/sh
  130 ?        R+     0:00 ps -ax
```

#### Upgrading the Connection with Your Language of Choice

For full interactive `exec` mode, you must have set these HTTP header fields within your `POST`:

* `Content-Type: text/plain`
* `Connection: upgrade`
* `Upgrade: tcp`

You must also pass in the following parameters via JSON:

| Field Name | Field Type | Value Required for Streaming |
|-----|-----|-----|
| attach-stderr | bool | true |
| attach-stdin | bool | true |
| attach-stdout | bool | true |
| cmd | array | ["{command}",...] |
| detach | bool | false |
| raw | bool | true |
| tty | bool | true |

When the ```POST``` arrives, the server will do a normal HTTP response and then hijack the stream.  If the `detach` parameter is set to `true`, the server will return a normal status response and disconnect the session immediately.

In case of hijacking, the server will copy all bytes from the HTTP request into stdin (of the executed process) and multiplex stdout/stderr onto the HTTP response. This multiplexing uses the same protocol that is used by `docker exec`.

## Connection Methods {#connection}

The following method tests connections to the API. It will respond with OK when the API is up and essential infrastructure components are reachable. It will return FAILED if the API is up, but not all essential infrastructure components are reachable.

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/ping | [GET](#Ping) | Ping Giant Swarm's API |

### Ping the API Connection {#Ping}

To ping the API connection, call the `GET` method on the `/v1/ping` endpoint.

```nohighlight
curl -sS https://api.giantswarm.io/v1/ping
```

#### Example with TEXT Response

```nohighlight
curl -sS https://api.giantswarm.io/v1/ping

"OK"
```

#### Response Status Codes

| Code | Type | Message |
|-----|-----|-----|
| 200 | string | 'OK'. |
| 500 | string | 'FAILED'.|

## User Methods {#user}

The user methods live under the `/v1/user/` endpoint. Where required, the `{user}` parameter indicates the value of the user's `username`.

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/user/\{user\}/login | [POST](#LoginUser) | Login as an existing user. |
| /v1/token/logout | [POST](#LogoutUser) | Logout. This will invalidate the token of the current request. |
| /v1/user/me/email/update | [POST](#UpdateEmail) | Update the email address of the currently logged in user. |
| /v1/user/me/password/update | [POST](#UpdatePassword) | Update the password of the currently logged in user. |
| /v1/user/me | [GET](#GetMe) | Get information about the currently logged in user. |
| /v1/user/me/memberships | [GET](#GetMemberships) | Get the names of the organizations the currently logged in user is a member of. |

### Generate Token {#LoginUser}

To generate a new authentication token, do a `POST` to the `/v1/user/{user}/login` endpoint with a JSON object containing the user's password:

```nohighlight
curl -sS \
    -X POST \
    -H "Content-Type: application/json" \
    --data '{"password":"'"$(echo -n <password> | base64)"'"}' \
    https://api.giantswarm.io/v1/user/{user}/login
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Content-Type: application/json" \
    --data '{"password":"'"$(echo -n f00bar | base64)"'"}' \
    https://api.giantswarm.io/v1/user/terminal/login \
    | python -mjson.tool

{
  "data": {
    "Id": "e5239484-2299-41df-b901-d0568db7e3f9",
  },
  "status_code": 10000,
  "status_text": "success"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The resource data was returned. |
| 400 | 10010 | The request was not authenticated. |

### Expire Token {#LogoutUser}

To expire an authentication token, do a `POST` to the `/v1/token/logout` endpoint with a JSON object containing the user's password:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    https://api.giantswarm.io/v1/token/logout
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    https://api.giantswarm.io/v1/token/logout \
    | python -mjson.tool

{
  "status_code": 10007,
  "status_text": "resource deleted"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10007 | The token was deleted. |
| 401 | n/a | The request was unauthorized. |

### Update User's Email Address {#UpdateEmail}

To update a user's email address, do a `POST` to the `/v1/user/me/email/update/` endpoint with a JSON payload containing the old and new email address:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    --data '{"old_email":"{old_email}","new_email":"{new_email}"}' \
    https://api.giantswarm.io/v1/user/me/email/update
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    --data '{"old_email":"terminal@giantswarm.io","new_email":"aee@giantswarm.io"}' \
    https://api.giantswarm.io/v1/user/me/email/update \
    | python -mjson.tool

{
  "status_code": 10006,
  "status_text": "resource updated"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10006 | The email was updated. |
| 400 | 10010 | The old email address did not match the address on record. |
| 401 | n/a | The request was unauthorized. |

### Update User's Password {#UpdatePassword}

To update a user's password, do a `POST` to the `/v1/user/me/password/update/` endpoint with a JSON payload containing the old and new password:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    --data '{"old_password":"{old_password}","new_password":"{new_password}"}' \
    https://api.giantswarm.io/v1/user/me/password/update
```

*Note: Changing a user's password will expire all tokens from the user's account.*

#### Example with JSON Response

This example uses the `echo` command with a `-n` piped through `base64` to generated the strings necessary for setting a new password:

```nohighlight
curl -sS \
    -X POST \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    --data '{"old_password":"'"$(echo -n f00bar | base64)"'","new_password":"'"$(echo -n bazf00 | base64)"'"}' \
    https://api.giantswarm.io/v1/user/me/password/update \
    | python -mjson.tool

{
  "status_code": 10006,
  "status_text": "resource updated"
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10006 | The password was updated. |
| 400 | 10010 | The hash of the old password did not match the hash on record. |
| 401 | n/a | The request was unauthorized. |

### Show User's Status {#GetMe}

To show a user's status, do a `GET` to the `/v1/user/me/` endpoint:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    https://api.giantswarm.io/v1/user/me
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    https://api.giantswarm.io/v1/user/me \
    | python -mjson.tool

{
  "status_code": 10000,
  "status_text": "success",
  "data": {
    "username": "terminal",
    "email": "terminal@giantswarm.io"
  }
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The resource data was returned. |
| 401 | n/a | The request was unauthorized. |

### Show User's Memberships {#GetMemberships}

To show a user's organization memberships, do a `GET` to the `/v1/user/me/memberships` endpoint:

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm {token}" \
    -H "Content-Type: application/json" \
    https://api.giantswarm.io/v1/user/me/memberships
```

#### Example with JSON Response

```nohighlight
curl -sS \
    -X GET \
    -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
    -H "Content-Type: application/json" \
    https://api.giantswarm.io/v1/user/me/memberships \
    | python -mjson.tool

{
  "status_code": 10000,
  "status_text": "success",
  "data": [
    "terminal",
    "giantswarm",
    "bantic"
  ]
}
```

#### Response Status Codes

| Status Code | Response Code | Description |
|-----|-----|-----|
| 200 | 10000 | The resource data was returned. |
| 401 | n/a | The request was unauthorized. |

### The End.
