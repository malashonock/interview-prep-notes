---
tags: [HTML/img, HTML/picture, Responsive]
title: 'HTML: Responsive images'
created: '2023-10-05T07:43:07.293Z'
modified: '2023-10-05T08:46:42.571Z'
---

# HTML: Responsive images

[MDN guide](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)

Common problems:
- **Resolution switching**: preserving physical size of an image on different devices with various screen resolutions
- **Art direction**: cropping/repositioning an image on narrow screens to keep important details in view

There are 2 basic `img` attributes which must _always_ be set (regardless of any techniques applied):
- `src`
- `alt`

On top of the above, various other attributes can be applied, depending on the technique used.

All techniques below are used for the browser to load _one_ image which is most appropriate to the device.


## Resolution switching

The approach is as follows:
- set the `srcset` attribute: a comma-separated list of space-separated entries of the following structure:
  - image URL
  - a width (`w`) or density (`x`) descriptor
- if width descriptors are used, set the `sizes` attribute: a comma-separated list of space-separated entries of the following structure:
  - (optional) media query (e.g. `(max-width: 786px)`)
  - width of the slot (in `px` or `vw`, but NOT in `%`) that will be filled when media query triggers

What the browser will do:
- work out which media query matches
- look at the slot size specified for the matching media query
- pick up an image with the least width equal to, or greater than, the width of the slot


**Example 1: Using width descriptors**
```
<img
  srcset="
    image-480w.jpg 480w, 
    image-800w.jpg 800w
  "
  sizes="
    (max-width: 600px) 480px,
    800px
  "
  src="image-800w.jpg"
  alt="Image description"
/>
```
**NB!** `w` in a width descriptor is the **intrinsic** width of an image in pixels.

**Example 2: Using density descriptors**
```
<img
  srcset="
    image-480w.jpg, 
    image-960w.jpg 2x
  "
  src="image-960w.jpg"
  alt="Image description"
/>
```
Density desriptor = how many _device pixels_ a device has in one _CSS pixel_.

**Tip**: `1x` is the default and can be omitted.


## Art direction

The approach is as follows:
- wrap the default `<img>` in a `<picture>` element
- before `<img>`, specify `<source>` elements that correspond to different image sizes

`<source>` attributes:
- `media`: the same as the media query in the `sizes`
- `srcset`: the URL to the image variant to be loaded for this media query

**Example**:
```
<picture>
  <source 
    media="(max-width: 799px)"
    srcset="image-480w-portrait.jpg"
  />
  <source 
    media="(min-width: 800px)"
    srcset="image-800w-landscape.jpg"
  />
  <img
    src="image-800w-landscape.jpg"
    alt="Image description"
  />
</picture>
```
