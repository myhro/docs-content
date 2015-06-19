+++
title = "API Changelog"
description = "Description of all changes affecting the public Giant Swarm API, in reverse chronological order."
date = "2015-06-02"
type = "page"
categories = ["Reference", "API"]
tags = ["api"]
weight = 100
+++

# API Changelog

Here we list all changes affecting the mechanics of our public [API](/reference/api/), including new functionality and bug fixes, in reverse chronological order.

## 2015-06-02

* The `LoginUser` route at `/v1/user/{nameOrEmail}/login` now returns only the `Id` field in its `data` response.

## 2015-06-19

* The `UpdateComponent` route at `/v1/company/{company}/env/{env}/app/{app}/service/{service}/component/{component}/version/{version}/update` now could optionally include in body the `rolling update strategy` to use, e.g. `{"strategy": "one-by-one"}`.
