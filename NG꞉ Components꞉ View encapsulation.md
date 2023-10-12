---
tags: [Angular/components]
title: 'NG: Components: View encapsulation'
created: '2023-10-12T08:27:58.260Z'
modified: '2023-10-12T09:09:18.213Z'
---

# NG: Components: View encapsulation

[Angular docs](https://angular.io/guide/view-encapsulation)

Component style encapsulation is configured via `@Component.encapsulation`.

Available modes:
- `ViewEncapsulation.Emulated` (default)
- `ViewEncapsulation.ShadowDom`
- `ViewEncapsulation.None`


## `ViewEncapsulation.Emulated`

The default mode.

_Emulates_ shadow DOM encapsulation by **post-processing** component CSS styles as follows:
- host element and its selector is appended with `[_nghost-...]` attribute
- child elements and their selectors are appended with `[_ngcontent-...]` attribute selector
- the appended attributes are generated at runtime and are unique for this component
- "scoped" styles are added to `<head>` of the document, but their selectors affect only elements within the respective components' templates


## `ViewEncapsulation.ShadowDom`

NG uses the built-in shadow DOM API.

Component styles are enclosed under the shadow root (=host element) and are isolated from the "light" DOM.

**NB!** The styles of `ViewEncapsulation.Emulated` and `ViewEncapsulation.None` child components are copied by NG to the shadow DOM host of each parent `ViewEncapsulation.ShadowDom` component &mdash; to make `ViewEncapsulation.None`components be rendered the same inside and outside of `ViewEncapsulation.ShadowDom` components.

**NB!** Styles from `ViewEncapsulation.ShadowDom` component are added to the shadow host **after** styles from `Emulated` and `None` child components, so they **override** global styles.


## `ViewEncapsulation.None`

NG does NOT post-processes the CSS in any manner, so all CSS styles declared for such component are, in effect, **global** and can affect any HTML element in the app.

NG adds such styles "as is" to the `<head>` of the document.


## View encapsulation modes interaction

**NB!** Mixing different encapsulation modes for different components is NOT recommended.


### `None` inside `Emulated`

**Note**: What follows below mostly regards styling native HTML elements.

`Emulated` parent's styles won't affect the `None` child's elements:
  - NG adds appends custom attributes only to `Emulated` parent's elements
  - however, elements inside the `None` child are NOT tagged with `Emulated` parent attributes
  - therefore, `Element` parent's styles can't reach `None` child's elements


### `None` and `Emulated` inside `ShadowDom`

`Emulated` children's styles are not overridden by `ShadowDom` parent styles.

**NB!** `None` children's styles ARE overridden by `ShadowDom` parent styles. 

