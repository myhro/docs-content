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

The Giant Swarm API is a simple, mostly RESTful JSON based API, which can be accessed over an encrypted HTTPS connection. The API is used by the Giant Swarm ```swarm``` CLI command and should be incorporated into your software where programatic control over your infrastructure is required.

*Note: The Giant Swarm API and this documentation is still in alpha and should be expected to change over time.*


## The Basics
The following information should get you familiarized with the use of the Giant Swarm API. If it fails to help you properly, [please open a ticket](https://github.com/giantswarm/docs-content/issues) and let us know how we can improve it.

### Resource URLs
All Giant Swarm API resources currently live under the following URL:

    https://api.giantswarm.io/v1/

#### Viewing API Methods via the CLI
The [Giant Swarm CLI](/reference/installation/) can use the `--debug` flag to show the API methods it is using. This can be useful in determining which API method should be used for a particular use case.

Here is an example which lists all applications in the ```dev``` environment:

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

### Authentication
The Giant Swarm API requires authentication to most of its URL endpoints. Authentication to the system may be done by POSTing JSON data to the ```/v1/user/<username>/login``` endpoint. Note the use of the ```application/json``` content type for the ```POST``` and the use of ```-n``` inside the escaped bash partial in the JSON.

    curl -H "Content-Type: application/json" -X POST -d '{"password":"'"$(echo -n <password> | base64)"'"}' https://api.giantswarm.io/v1/user/<username>/login

*Note: Be careful when using the ```base64``` command to generate encoded data from STDIN. For example, the default behavior of ```echo foo | base64``` in bash will result in a line feed ```'/n'``` being encoded in the password. Giant Swarm's API is sorry that it is not able authorize a password with an appended line feed or carriage return.*

Let's take a look at an example which uses Python to clean up the output:

```
$ curl -s -H "Content-Type: application/json" -X POST -d '{"password":"'"$(echo -n f00bar | base64)"'"}' https://api.giantswarm.io/v1/user/bant/login | python -mjson.tool

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

#### Using a Token
Tokens for use with API calls may be obtained by requesting the ```/v1/user/<username>/login``` endpoint, as shown above. The token is identified by the ```Id``` key in the response. In the example above, the token would be:

    e5239484-2299-41df-b901-d0568db7e3f9
    
This token must be passed in the headers of the API request. In this example ```curl``` request, we set the auth request header to ```Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9```. Please note the string '**giantswarm**' is always required, and should not be substituted for other strings.

Here's an example of using a token to ```curl``` the ```/v1/org/bant/env/``` endpoint to list available environments:

```
$ curl -s -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" https://api.giantswarm.io/v1/org/bant/env/ | python -mjson.tool
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

#### Processing Reponses with bash
You may occasionally wish to prototype API calls in ```bash```. Here's an example which uses the ```jq``` command to extract the required data:

```
curl -s -H "Authorization: giantswarm e5239484-2299-41df-b901-d0568db7e3f9" https://api.giantswarm.io/v1/org/bant/env/ | jq '.data.environments[0].name'

"dev"
```

*Note: If you are running on OSX, you may install the ```jq``` command by doing a ```brew install jq```. You can find more information about ```jq``` [here](http://stedolan.github.io/jq/)*.

