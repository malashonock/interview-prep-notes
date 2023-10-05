---
tags: [HTML/link, Performance]
title: 'HTML: link: Content preloading'
created: '2023-10-05T08:49:12.394Z'
modified: '2023-10-05T09:25:46.858Z'
---

# HTML: link: Content preloading

All `<link>`s are placed in `<head>`.


## `preload`

```
<link 
  rel="preload" 
  href="url/to/resource" 
  as="resource-type" // allows browser to prioritize
  type="mime-type" // for multimedia
  media="(media-query)" // responsive preloading
/>
```

_Orders_ the browser to fetch and cache the resource _ASAP_ after the page starts loading.

Features:
- doesn't execute the resource2, only caches it
- the command is **mandatory** for the browser

**Use case**: when a resource will likely be needed soon after, or even during,the page load, e.g.:
- non-standard fonts mentioned in a CSS stylesheet
- critical CSS (if separated)
- larger images or videos
- resources JS might likely request (JSON, etc.)


## `prefetch`

```
<link 
  rel="prefetch" 
  href="url/to/resource" 
  ... // other attributes (see above)
/>
```

_Asks_ the browser to fetch and cache the resource _when it's likely that it will be needed_.

Features:
- doesn't execute the resource2, only caches it
- lower priority

**Use case**: when a resource will likely be needed for the next navigation away from the current page; NOT for urgent requests.


## `preconnect`

```
<link 
  rel="preconnect" 
  href="https://api.my-app.com" 
/>
```

Asks the browser to establish a connection with the specified domain ASAP:
- resolve DNS
- TCP handshake
- TLS handshake (for `https`)

Features:
- not mandatory for the browser

**Use case**: when the URL of a resource that will likely be needed soon is not known in advance, but at least we know its domain, e.g.:
  - API backend
  - Google Fonts (`fonts.googleapis.com` -> `fonts.gstatic.com`)


## `dns-prefetch`

Same as `preconnect`, but connection is limited only to DNS resolution.


## `prerender` (deprecated)

```
<link 
  rel="prerender" 
  href="https://my-app.com/price-list" 
/>
```

Asks the browser to load the specified page in the background.

Features:
- Chrome does a NoState prefetch, i.e. loads a page and its resources, but doesn't render it and doesn't execute JS scripts
- Most other browsers don't support it
- not mandatory for the browser

**Use case**: to render a page that the user will highly likely navigate to next.

**NB!** `prerender` feature is **deprecated**!

