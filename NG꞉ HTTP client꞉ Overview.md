---
tags: [Angular/HTTP]
title: 'NG: HTTP client: Overview'
created: '2023-09-12T09:21:42.864Z'
modified: '2023-10-04T13:38:22.693Z'
---

# NG: HTTP client: Overview

All `HttpClient` methods return **observables**.

Execution of HTTP requests is _deferred_ &mdash; you need to subscribe to the returned observable.

All `HttpClient` observables are **cold**, i.e. each subscription triggers a new request.




