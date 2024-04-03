---
tags: [HTTP]
title: 'HTTP: CORS'
created: '2024-03-29T11:56:38.805Z'
modified: '2024-03-29T12:48:07.548Z'
---

# HTTP: CORS

[MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)


## CORS overview

CORS = Cross-Origin Resource Sharing

Origin = scheme + host + port

Cross-origin requests &mdash; requests to an origin that is different from the origin of the document which has initiated the request.

Unless configured otherwise, browsers block cross-origin requests for security reasons.

**NB!** For the same security reasons, the details about such errors are not visible to JS &mdash; only in browser console.

CORS is the HTTP header based mechanism that enables cross-origin requests.


## Simple vs. preflighted requests


