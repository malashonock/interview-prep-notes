---
tags: [Angular/directives]
title: 'NG: Directives: Custom attribute directives'
created: '2023-10-11T09:38:48.862Z'
modified: '2024-01-04T12:47:33.877Z'
---

# NG: Directives: Custom attribute directives


## Direct access to host element

1. Inject `ElementRef`
2. Inject `Renderer2`
3. Access the native DOM element via `ElementRef#nativeElement`
4. Modify element via [`Renderer2` API](https://angular.io/api/core/Renderer2), e.g.:
  - `setAttribute()` / `removeAttribute()`
  - `addClass()` / `removeClass()`
  - `setStyle()` / `removeStyle()`
  - `setProperty()`


## `@Directive.host`

Can be used to map component class fields to host element's properties, attributes and events.

Both keys and values in the object must be the same **strings** as those used in **class/style/attribute/event binding** in the template, e.g.:
  - `'[attr.disabled]': 'disabled'`
  - `'[class.valid]': 'control.valid'`
  - `'[style.width.px]': '500'`
  - `'(click)': 'onClick($event)'`

~~**Note**: it's recommended to use `@HostBinding()` and `@HostListener()` instead.~~ 
**Updated note:** [angular.dev](https://angular.dev/guide/components/host-elements) now recommends to use `@Directive.host` over `@HostBinding()` and `@HostListener()` wherever possible.


## `@HostBinding()`

[HostBinding API](https://angular.io/api/core/HostBinding)

A decorator for a component class field.  

Accepts the name of the host element's property, class, style or attribute.

Binds the component class field to the host element's property, class, style or attribute.

**Usage examples**:
- properties:
```
@HostBinding('id') public id: string;
```
- classes (must be prefixed with `class.`):
```
@HostBinding('class.valid`) public get valid { ... }
```
- styles (must be prefixed with `style.`):
```
@HostBinding('style.color') public fontColor: string;
@HostBinding('style.width.px') public width = 500;
```
- attributes (must be prefixed with `attr.`):
```
@HostBinding('attr.aria-required`) public required: boolean;
```


## `HostListener()`

[HostListener API](https://angular.io/api/core/HostListener)

A decorator for a component class method.

Arguments:
- `eventName`: the DOM event to listen for:
  - without any prefix = targets the host element
  - prefixes can be used to change the default target:
    - `window:`
    - `document:`
    - `body:`
- `args`: an array of argument names (**strings**) that should be passed to the component class method

**Usage examples**:
```
@HostListener('click', ['$event.target'])
public onClick(btn: HTMLButtonElement): void { ... }

@HostListener('document:keydown.enter', ['$event'])
public onEnterPressed(event: KeyboardEvent): void { ... }
```

**NB!** If the decorated method returns **false**, `preventDefault()` will be applied.
