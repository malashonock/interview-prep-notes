---
tags: [HTTP]
title: 'HTTP: CORS'
created: '2024-03-29T11:56:38.805Z'
modified: '2024-04-03T08:32:37.278Z'
---

# HTTP: CORS

[MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
[Статья на Хабре](https://habr.com/ru/companies/macloud/articles/553826/)


## CORS overview

CORS = Cross-Origin Resource Sharing

Origin = scheme + host + port

**Cross-origin requests** &mdash; requests to an origin that is different from the origin of the document which has initiated the request.

Unless configured otherwise, browsers **block** cross-origin requests for security reasons.

**NB!** It's the user's browser who enforces CORS, i.e. allows or blocks sending a request or processing the response.

**NB!** For the same security reasons, the details about such errors are not visible to JS &mdash; only in browser console.

CORS is the HTTP header based mechanism that enables cross-origin requests.


## Simple vs. preflighted requests


### Simple CORS requests

**Simple request** criteria (_most common_):
- HTTP method is non-mutating:
  - `HEAD`
  - `GET`
  - `POST` (!)
- Request headers: only those set by the browser automatically, the only manual ones allowed are:
  - `Accept`
  - `Accept-Language`
  - `Content-Language`
  - `Content-Type`, the only allowed types are:
    - `application/x-www-form-urlencoded`
    - `multipart/form-data`
    - `text/plain`

All other CORS requests are preflighted.

Simple requests DON'T trigger a preflight request.

Data flow is simple:
1. The browser appends **`Origin`** request header with the origin from which the request is being sent
2. The server appends ***`Access-Control-Allow-Origin`*** response header with the origin(s) from which the server allows requests
3. The browser reads the above response header and decides if it may proceed with handling the response


### Preflighted CORS requests

**Preflighted request** flow is as follows:
1. The browser sends an `OPTIONS` request with roughly the following headers:
  - `Origin` (always)
  - `Access-Control-Request-Method`: asks the server if it supports the method of the intended request
  - `Access-Control-Request-Headers`: asks the server if it supports the specified headers
2. The server sends a `204 No Content` response with roughly the following headers:
  - `Access-Control-Allow-Origin` (always)
  - `Access-Control-Allow-Methods`: the list of methods supported by the server
  - `Access-Control-Allow-Headers`: the list of headers supported by the server
  - `Access-Control-Max-Age`: how long the preflight response is valid and can be cached (5s by default)
3. The browser reads the preflight response and decides if it may proceed with sending the intended request
4. The browser sends the intended request, appending (amongst other things) `Origin` and the headers specified in the preflight request
5. The server sends the actual response, adding (amongst other things) `Access-Control-Allow-Origin`


## Requests with credentials

Credentials = `Cookie`

By default, `fetch()` does NOT append credentials.  
To make it do so, pass `credentials: 'include'` into the request options.


### Simple CORS requests with credentials

The flow is the same, with the following additions:
1. The browser appends the `Cookie` request header with the cookies applicable to the target server
2. The server appends the `Access-Control-Allow-Credentials` response header which specifies if the server allows CORS requests with credentials
3. Unless `Access-Control-Allow-Credentials: true`, the browser blocks further handling the response


### Preflighted CORS requests with credentials

1. The preflight `OPTIONS` request must NOT include credentials!
2. However, the preflight response MUST specify `Access-Control-Allow-Credentials`
3. If `Access-Control-Allow-Credentials: true`,  (and other CORS conditions satisfied), the browser proceeds with the intended request, appending the credentials thereto.

**NB!** Special rules for `Access-Control-Allow-Credentials: true`:
- `Access-Control-Allow-Origin: *` is not allowed &mdash; origin(s) must be specified explicitly
- `Access-Control-Allow-Methods: *` is not allowed &mdash; methods must be specified explicitly
- `Access-Control-Allow-Headers: *` is not allowed &mdash; headers must be specified explicitly


