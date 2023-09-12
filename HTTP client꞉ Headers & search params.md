---
tags: [Angular/HTTP]
title: 'HTTP client: Headers & search params'
created: '2023-09-12T10:15:11.027Z'
modified: '2023-09-12T10:30:40.130Z'
---

# HTTP client: Headers & search params


## Working with headers

`headers` need to be constructed via `new HttpHeaders({ ... })`.

`headers` object is immutable &mdash; to update it, use `HttpHeaders#set()` method.

**Tip** `HttpHeaders#set()` method is _chainable_.


## Working with search params

`params` need to be constructed via `new HttpParams(...)`, passing one of the following args:
- `{ fromObject: { ... } }`
- `{ fromString: '...' }`: from query string, e.g. `'key=value'`  

`params` object is immutable &mdash; to update it, use `HttpParams#set()` method.

**Tip** `HttpParams#set()` method is _chainable_.
