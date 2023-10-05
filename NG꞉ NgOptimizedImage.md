---
tags: [Angular/optimizations, Performance]
title: 'NG: NgOptimizedImage'
created: '2023-10-05T06:53:34.176Z'
modified: '2023-10-05T07:39:11.326Z'
---

# NG: NgOptimizedImage


## Overview

[`NgOptimizedImage`](https://angular.io/guide/image-directive) directive does the following:
- automatically sets attributes on `img`;
  - [`fetchpriority`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/fetchPriority)
  - [`loading`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/loading) (`lazy` by default)
  - [`srcset`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/srcset)
- takes care of related `<link>`s in the `<head>`
  - warns if there is no `<link preconnect="..." />`
  - with SSR, generates `<link preload="..." />` hint
- enforces best practices:
  - requires `width` and `height` to be set (unless `fill` is set)
  - warns if `width` or `height` have been set incorrectly, i.e. aspect ratio is distorted as compared to intrinsic sizes
  - encourages the use of image CDNs

**Usage**: `NgOptimizedImage` is activated by setting `ngSrc` instead of the standard `src`.


## LCP images

For the LCP image, `priority` attribute should be set, which will have the following effect:
- `fetchpriority` will be set to `high`
- `loading` will be set to `eager`
- with SSR, `<link preload="..." />` is added to `<head>`


## Image sizing

**Option 1**: set `width` and `height` explicitly, to prevent layout shift.

Rules to follow:
- for **fixed-size** images, set the _desired_ width and height to be displayed, BUT the aspect ratio should be the same as the intrinsic aspect ratio!
- for **responsive** images, set the _intrinsic_ width and height, AND set the [`sizes`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/sizes) attribute

**Option 2**:
- wrap the `img` in a **CSS-positioned** container
- instead of `width` and `height`, set the **`fill`** attribute on the `img`
- fine-tune `img` styling with CSS [`object-fit`](https://developer.mozilla.org/en-US/docs/Web/CSS/object-fit) or [`object-position`](https://developer.mozilla.org/en-US/docs/Web/CSS/object-position)


## Responsive images

For fixed-size images, there's no need to set `sizes` &mdash; `srcset` will be generated automatically using _density descriptors_, e.g. `<img ... srcset="image-400w.jpg 1x, image-800w.jpg 2x" />`.

For responsive images, `sizes` should be set as per the HTML spec, e.g. `sizes="(max-width: 768px) 100vw, 50vw"`.


## Image loaders

A _loader_ is a fn that generates in image transformation URL for a given image config.

Loader types:

### Generic loader 

Default.  
Applies no URL transformations

### 3rd party loaders

Built-in, need to be provided explicitly, e.g.:
```
providers: [
  provideCloudflareLoader('https://my.base.url'),
]
```

### Custom loaders

Provided under `IMAGE_LOADER` token as follows:
```
providers: [
  {
    provide: IMAGE_LOADER,
    useValue: (config: ImageLoaderConfig) => {
      return `https://example.com/images?src=${config.src}&width=${config.width}`;
    }
  }
]
```

`ImageLoaderConfig` props:
- `src`
- `width` (optional)
- `loaderParams` (optional): arbitrary dictionary

To set `loaderParams`, bind an object value to `[loaderParams]` on the `img`.


