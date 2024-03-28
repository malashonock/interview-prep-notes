---
tags: [Security]
title: 'NG: Security: Built-in protections'
created: '2024-03-28T08:28:54.381Z'
modified: '2024-03-28T10:03:17.350Z'
---

# NG: Security: Built-in protections

[Angular docs](https://angular.io/guide/security)


## XSS

[DomSanitizer API](https://angular.io/api/platform-browser/DomSanitizer)

Whenever a value is inserted to the DOM via **template binding or interpolation**, it gets sanitized (=escaped) automatically.

**NB!** For that protection to work:
- use NG templates where possible
- always use AOT compilation in production
- never create templates by concatenating user input with template syntax

Default sanitization does **NOT** occur in the following cases:
- direct DOM manipulation
- using 3rd party libs that can manipulate DOM directly


### Manual sanitization

1. inject a `DomSanitizer` instance
2. to escape a value, wrap it into a call to `DomSanitizer#sanitize()`, passing it a `SecurityContext`

[`SecurityContext`](https://angular.io/api/core/SecurityContext) is important, because there are different escaping rules depending on where the escaped string is used:
- `HTML`
- `STYLE`
- `URL` - in links, e.g. `<a href>`
- `RESOURCE_URL` - for loadable/executable assets, e.g. `<script src>`


### Bypassing trusted values

If need be to include executable code, and you genuinely trust it, use `bypassSecurityTrust***` methods with the appropriate security context:
- `bypassSecurityScript`
- `bypassSecurityStyle`
- `bypassSecurityUrl`
- `bypassSecurityResourceUrl`


### Content Security Policy

[CSP in Angular](https://borstch.com/blog/development/angular-and-content-security-policy-csp-best-practices)  
[Medium article](https://blog.stackademic.com/whats-a-nonce-7508458bded9)

Problem with NG &mdash; component styles are inlined, so a misconfigured CSP can prevent them from being applied by the browser.

**NB!** Nonce-based CSP is traditionally configured on the server! (with NG SSR, or on Nginx/whatever with CSR)

2 ways to apply CSP nonce in NG:
- apply `ngCspNonce="..."` attribute on root NG element
- at runtime: provide **`CSP_NONCE`** token via `useValue: ...` in `bootstrapApplication` options / root `NgModule`'s `providers`

**NB!** If nonce cannot be generated/passed to NG app, inline styles can be allowed by using `style-src 'unsafe-inline'` directibe &mdash; but it's considered bad practice!


## XSRF

[Angular docs](https://angular.io/guide/http-security-xsrf-protection)

1. NG reads the cookie with key `XSRF-TOKEN`
2. `HttpClient` sets `X-XSRF-TOKEN` HTTP header:
- on all mutating requests (POST, PUT, DELETE) to relative URLs
- NOT on GET/HEAD requests
- NOT on absolute URLS

**NB!** This is only half of the XSRF protection scheme &mdash; the server must be configured to set the cookie and to verify the `X-XSRF-TOKEN` header.


## XSSI

NG `HttpClient` automatically strips the string `")]}',\n"` (commonly prepended by JSON web APIs) from all JSON responses.

