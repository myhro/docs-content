+++
title = "Giant Swarm API Reference"
description = "The Giant Swarm API allows you to create, controll and monitor applications programmatically. The reference contains descriptions of all building blocks."
date = "2015-05-04"
type = "page"
categories = ["Reference", "API"]
tags = ["api"]
weight = 100
+++


# The Giant Swarm API
# remove your key kord

The Giant Swarm API is a simple, mostly RESTful JSON based API, which can be accessed over an encrypted HTTPS connection. The API is used by the Giant Swarm ```swarm``` CLI command and should be incorporated into your software where programatic control over your infrastructure is required.

*Note: The Giant Swarm API and this documentation is still in alpha and should be expected to change over time.*


## The Basics
The following information should get you familiarized with the use of the Giant Swarm API.  [Please open a ticket](https://github.com/giantswarm/docs-content/issues) and let us know how we can improve upon it.

The following types of management methods are supported by the Giant Swarm API, in addition to the [authentication methods](#auth):

1. [Organization](#org)
1. [Application](#app)
1. [Service](#service)
1. [Component](#component)
1. [Instance](#instance)
1. [Connection](#connection)
1. [User](#user)

### Resource URLs
All Giant Swarm API resources currently live under the following URL:

    https://api.giantswarm.io/
    
The API is versioned using the ```/v1/``` path:

    https://api.giantswarm.io/v1/
    
The ```/org/```, ```/env/``` and ```/app/``` path parameters denote the concepts of organizations, environments and applications within the Giant Swarm service. These are respectively combined with named objects within the system. 

Here is an example URL which requests information about the organization named ```bantinc```:

     https://api.giantswarm.io/v1/org/bantic/

### SDKs
No public SDKs exist for the Giant Swarm API at this time, but we are planning on adding them to this section when they become available. Please feel free to [comment on Discourse](http://discourse.giantswarm.io/t/sdks-for-the-giant-swarm-api/32) if you are interested in developing an SDK in your favorite language!

### Response Codes
Giant Swarm's API returns the standard HTTP status codes. The API also uses a set of internal response codes:

| Response Code | Response Type |
|-----|------|-----|
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
The [Giant Swarm CLI](/reference/installation/) can be used with the `--debug` flag to show the methods it uses when it talks to the API.

Here is an example which lists all applications in the ```bantic``` organization and the ```dev``` environment:

```
$ swarm --debug ls
DEBUG: GET https://api.giantswarm.io/v1/org/bant/env/dev/app/
DEBUG: << 200 OK
DEBUG: GET https://api.giantswarm.io/v1/org/bant/env/dev/app/currentweather/status
DEBUG: << 200 OK
1 application available in environment 'bant/dev':

application     created              status
currentweather  2015-04-28 17:28:36  up
```

In this example, we can see we should use a ```GET``` method on the ```/v1/org/bant/env/dev/app/``` endpoint to get a list of applications in our ```dev``` environment.

<a name="auth"></a>
## Authentication
The Giant Swarm API requires authentication for most of its URL endpoints. Authentication to the APIs may be done by POSTing to ```/v1/user/{username}/login``` endpoint while using the ```application/json``` content type for the ```POST```. 

Here's an example which uses ```curl``` to ```POST``` JSON and a bash partial using ```echo``` with the ```-n``` (no line feed) option to populate the password dynamically:

    curl -H "Content-Type: application/json" -X POST \
    -d '{"password":"'"$(echo -n <password> | base64)"'"}' \
    https://api.giantswarm.io/v1/user/<username>/login

*Note: This example uses brackets ```<>``` to distinguish the substitution of password and usernames in the JSON based request. The rest of this document uses braces ```{}``` to denote the use of string substitutions by the user.*

Let's take a look at an example which uses Python to clean up the output:

```
$ curl -sS -H "Content-Type: application/json" -X POST \
-d '{"password":"'"$(echo -n f00bar | base64)"'"}' \
https://api.giantswarm.io/v1/user/bant/login | python -mjson.tool

{
    "data": {
        "Created": "2015-05-09 20:03:29.523713815 +0000 UTC",
        "Id": "e5239484-2299-41df-b901-d0568db7e3f9",
        "UserId": "31928724-522b-4941-bb6b-35286622ff65"
    },
    "status_code": 10000,
    "status_text": "success"
}
```

*Note: Be careful when using the ```base64``` command to generate encoded data from STDIN. For example, the default behavior of ```echo foo | base64``` in bash will result in a line feed ```'/n'``` being encoded in the password. Giant Swarm's API will not authorize a password with an appended line feed or carriage return.*

#### Using a Token
Tokens for use with API calls may be obtained by requesting the ```/v1/user/{username}/login``` endpoint, as shown above. The token is identified by the ```Id``` key in the response. In the example above, the token would be:

    e5239484-2299-41df-b901-d0568db7e3f9
    
This token must be passed in the headers of the API request. In this example ```curl``` request, we set the auth request header to ```Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9```. Please note the string '**giantswarm**' is always required, and should not be substituted for other strings.

Here's an example of using a token to ```curl``` the ```/v1/org/bant/env/``` endpoint to list available environments:

```
$ curl -sS \
-H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
https://api.giantswarm.io/v1/org/bant/env/ | python -mjson.tool

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

Tokens may also be retrieved from the command line if the ```swarm``` client is installed and authenticated:

    $ cat ~/.swarm/token; echo;
    23f0097e-10cf-4076-b02d-087c0090e73d

    
#### Processing Reponses with bash
You may occasionally wish to prototype API calls in ```bash```. Here's an example which uses the ```jq``` command to extract the required data from the JSON response data:

```
curl -sS \
-H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
https://api.giantswarm.io/v1/org/bant/env/ | jq '.data.environments[0].name'

"dev"
```

*Note: If you are running on OSX, you may install the ```jq``` command by doing a ```brew install jq```. You can find more information about ```jq```, including install information for Linux, [on its website](http://stedolan.github.io/jq/)*.

<a name="org"></a>
## Organization Methods
The organization methods live under the ```/v1/org/``` endpoint. The following table lists the available methods for operating on organizations:

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/env/ | [GET](#ListEnvironments) | Get the environments within the given organization that contain applications. |
| /v1/org/ | [POST](#CreateOrganization) | Create a new organization. |
| /v1/org/\{org\}/ | [DELETE](#DeleteOrganization) | Remove an organization. |
| /v1/org/\{org\} | [GET](#GetOrganization) | Get information about an organization. |
| /v1/org/\{org\}/members/add | [POST](#AddOrganizationMember) | Add a member to an organization. |
| /v1/org/\{org\}/members/remove | [POST](#RemoveOrganizationMember) | Remove a member from an organization. |

In addition to the path parameters shown above, the following JSON POST parameters are used by the organization methods:

| Key | Value Type | Description | Required? |
|-----|-----|-----|-----|
| org_id | string | The new organization's name. | Yes |
| username | string | The username for the new user. | Yes |

### Listing an Organization's Environments
To request a list of an organization's environments, call the ```GET``` method on the ```/v1/org/{org}/env/``` endpoint:

```
curl \ 
-H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
https://api.giantswarm.io/v1/org/bant/env/
```

##### Example with JSON Response

```
$ curl -s -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
https://api.giantswarm.io/v1/org/bant/env/ | python -mjson.tool

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

### Create a New Organization
To create a new organization in an account, call the ```POST``` method on the ```/v1/org/``` endpoint:

```
curl -sS \
-X POST \
-H "Authorization: giantswarm 23ffb97e-1dcf-4776-b52d-087cbb90e73d" \
-H "Content-Type: application/json" \
-H "Accept: application/json" \
--data {\"org_id\":\"bantic\"} \
https://api.giantswarm.io/v1/org/
```
*Note: The default organization name will initially be the username of the account.*

##### Example with JSON Response

```
$ curl -sS \
-X POST \
-H "Authorization: giantswarm 23ffb97e-1dcf-4776-b52d-087cbb90e73d" \
-H "Content-Type: application/json" \
-H "Accept: application/json" \
--data {\"org_id\":\"bantic\"} \
https://api.giantswarm.io/v1/org/ | python -mjson.tool

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

### Remove an Organization
To remove an existing organization, call the ```DELETE``` method on the ```/v1/org/{org_id}/``` endpoint:

```
curl -sS \
-X DELETE \
-H "Authorization: giantswarm 23ffb97e-1dcf-4776-b52d-087cbb90e73d" \
https://api.giantswarm.io/v1/org/{org_id}/
```

*Note: You must use a trailing slash on the end of the URL when the path parameter is an object.*

##### Example with JSON Response

```
$ curl -sS \
-X DELETE \
-H "Authorization: giantswarm 23ffb97e-1dcf-4776-b52d-087cbb90e73d" \
https://api.giantswarm.io/v1/org/bantic/ | python -mjson.tool

{
  "status_code": 10007,
  "status_text": "resource deleted"
}
```

### Get Information on Organization

### Add a Member to an Organization

### Remove a Member from an Organization


<a name="app"></a>
## Application Methods
The operations methods for applications live under the ```/v1/org/{org}/env/{env}/app/``` endpoints. The following table lists the available methods for operating on applications.

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/env/\{env\}/app/ | [GET](#ListApps) | Return a list of all applications running in a specific environment. |
| /v1/org/\{org\}/env/\{env\}/app/ | [POST](#CreateApp) | Create a new application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\} | [DELETE](#DeleteApp) | Delete an existing application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/start | [POST](#StartApp) | Start an existing application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/stop | [POST](#StopApp) | Stop an existing application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/status | [GET](#StatusApp) | Query the status of an application. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/config | [GET](#ConfigApp) | Get the configuration of an application. |

### List Running Applications

### Create a New Application

### Delete an Application

### Start an Application

### Stop an Application

### Get Application's Configuration

<a name="service"></a>
## Service Methods

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/start | [POST](#StartService) | Start a service. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/stop | [POST](#StopService) | Stop a service. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/status | [GET](#StatusService) | Get the status of an existing service. |

<a name="component"></a>
## Component Methods

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/<br/>component/\{component\}/version/\{version\}/update | [POST](#UpdateComponent) | Update the docker image version of an existing component. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/<br/>component/\{component\}/scaleup/\{scale\} | [POST](#ScaleUpComponent) | Add instances of an existing component. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/<br/>component/\{component\}/scaledown/\{scale\} | [POST](#ScaleDownComponent) | Remove instances of an existing component. |
| /v1/org/\{org\}/env/\{env\}/app/\{app\}/service/\{service\}/<br/>component/\{component\}/status | [GET](#StatusComponent) | Query the status of an existing component. |

<a name="instance"></a>
## Instance Methods

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/org/\{org\}/instance/\{id\}/logs | [GET](#LogInstance) | Get logs of an instance. |
| /v1/org/\{org\}/instance/\{id\}/stats | [GET](#StatInstance) | Get statistics of an instance. |
| /v1/org/\{org\}/instance/\{id\}/exec | [GET](#ExecInstance) | Execute a command inside an instance or opens a shell for remote access. |

<a name="connection"></a>
## Connection Methods

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/ping | [GET](#Ping) | Test connections to the API. It will respond with OK when the API is up and essential infrastructure components are reachable. It will return FAILED if the API is up, but not all essential infrastructure components are reachable. |

<a name="ping""></a>
### Test the API Connection
To request a list of an organization's environments, call the ```GET``` method on the ```/v1/org/{org}/env/``` endpoint:

```
$ curl -s -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" \
https://api.giantswarm.io/v1/ping
"OK"
```

The response will be one of two text strings: [```"OK"```, ```"FAILED"```].

##### Example Response

```
"OK"
```

##### Response Status Codes

| Code | Type | Message |
|-----|-----|-----|-----|
| 200 | string | 'OK'. |
| 500 | string | 'FAILED'.|

<a name="user""></a>
## User Methods

| Resource Path | Operation | Description |
|-----|-----|-----|
| /v1/user/\{user\}/login | [POST](#LoginUser) | Login as an existing user. |
| /v1/token/logout | [POST](#LogoutUser) | Logout. This will invalidate all tokens of the currently logged in user. |
| /v1/user/me/email/update | [POST](#UpdateEmail) | Update the email address of the currently logged in user. |
| /v1/user/me/password/update | [POST](#UpdatePassword) | Update the password of the currently logged in user. |
| /v1/user/me | [GET](#GetMe) | Get information about the currently logged in user. |
| /v1/user/me/memberships | [GET](#GetMemberships) | Get the names of the organizations the currently logged in user is a member of. |



