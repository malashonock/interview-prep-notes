---
tags: [Angular/HTTP]
title: 'HTTP client: Error handling'
created: '2023-09-12T09:57:46.215Z'
modified: '2023-09-12T10:14:43.451Z'
---

# HTTP client: Error handling

If the request fails, `HttpClient` returns an `HttpErrorResponse` which implements (not extends!) `Error` (i.e. _doesn't throw an error_).

`HttpErrorResponse#error` prop contains either an `Error` object (=exception thrown) or an error response from the server.

Error response types:
- **server-side** errors:
  - `status`: HTTP status code (_e.g. 404 or 500_)
  - `error`: error response from the server
- **client-side** or **network** errors:
  - `status`: always `0`
  - `error`: will contain some `Error` object

**Tip**: catch errors in RxJs observables in `pipe` using `catchError` operator.


## Retrying HTTP requests

Use RxJs `retry(n)` operator in `pipe`:
```
this.http.get<Something>(url)
  .pipe(
    retry(3), // retry up to 3 times
    catchError(console.log) // then handle the error
  )
```



