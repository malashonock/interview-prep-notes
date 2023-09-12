---
tags: [Angular/HTTP]
title: 'HTTP client: Events'
created: '2023-09-12T09:31:29.383Z'
modified: '2023-09-12T09:56:29.731Z'
---

# HTTP client: Events


## [`HttpSentEvent`](https://angular.io/api/common/http/HttpSentEvent)

Use case: when a request is retried, to distinguish between retries.


## [`HttpHeaderResponse`](https://angular.io/api/common/http/HttpHeaderResponse)

Includes status and header, but no response body.

Props of interest:
- `url`
- `headers`
- `status`
- `statusText`
- `ok`


## [`HttpResponse<T>`](https://angular.io/api/common/http/HttpResponse)

Full response, including `T`-typed response body. 

Props of interest:
- `body`


## [`HttpProgressEvent`](https://angular.io/api/common/http/HttpProgressEvent)

Props of interest:
- `type`: `DownloadProgress` | `UploadProgress`
- `loaded`: number of bytes already transferred
- `total`?: total number of bytes (if available)


## [`HttpUserEvent`](https://angular.io/api/common/http/HttpUserEvent)

User-defined events

