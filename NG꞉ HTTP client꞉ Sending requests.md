---
tags: [Angular/HTTP]
title: 'NG: HTTP client: Sending requests'
created: '2023-09-12T09:56:47.509Z'
modified: '2023-10-04T13:38:26.347Z'
---

# NG: HTTP client: Sending requests


## Data fetching

`HttpClient#get()`

Method params:
1. `url`
2. `options` object

Available `options`:
- **`observe`**: specifies how much of the response to return
  - `'body'`: return only body
  - `'response'`: return full HTTP response
  - `'events'`: return the entire stream of HTTP events
- **`responseType`**: the format in which to return data
  - `'json'`
  - `'text'`
  - `'blob'`
  - `'arrayBuffer'`
- `headers`
- `params`: URL search (query) params
- `reportProgress`: track progress events?
- `withCredentials`: send cookies / authorization headers with the request?

**NB!**: When typing the response body, use _interfaces_ rather than classes, because the server returns a POJO, not a class instance.


## Mutations

`HttpClient#post()`
`HttpClient#put()`
`HttpClient#delete()`

**NB!** `post()` and `put()`, besides `url`, require a `body`.
