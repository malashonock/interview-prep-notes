---
tags: [Angular/HTTP]
title: 'HTTP client: Overview'
created: '2023-09-12T09:21:42.864Z'
modified: '2023-09-12T09:57:36.443Z'
---

# HTTP client: Overview

All `HttpClient` methods return **observables**.

Execution of HTTP requests is _deferred_ &mdash; you need to subscribe to the returned observable.

All `HttpClient` observables are **cold**, i.e. each subscription thriggers a new request.




