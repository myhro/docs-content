+++
title = "API Changelog"
description = "Description of all changes affecting the public Giant Swarm API, in reverse chronological order."
date = "2015-06-03"
type = "page"
categories = ["Reference", "API"]
tags = ["api"]
weight = 100
+++

# API Changelog

Here we list all changes affecting the mechanics of our public [API](/reference/api/), including new functionality and bug fixes, in reverse chronological order.

<div class="well">
<h4>Deprecation Notice</h4>
<p>Route <code>/v1/org/{org}/env/{env}/app/</code>: The output provides, per listed application, the two keys <code>org</code> and <code>company</code> with identical values. Please prepare for the legacy <code>company</code> key disappearing some time soon.
</div>


## 2015-06-02

* The `LoginUser` route at `/v1/user/{nameOrEmail}/login` now returns only the `Id` field in its `data` response. 

