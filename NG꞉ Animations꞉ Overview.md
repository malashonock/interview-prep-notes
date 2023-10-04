---
tags: [Angular/animations]
title: 'NG: Animations: Overview'
created: '2023-10-03T09:32:05.758Z'
modified: '2023-10-04T13:36:35.185Z'
---

# NG: Animations: Overview


## Links

[Introduction to Angular animations](https://angular.io/guide/animations)  
[Animation transitions and triggers](https://angular.io/guide/transition-and-triggers)  
[Complex animation sequences](https://angular.io/guide/complex-animation-sequences)  
[Reusable animations](https://angular.io/guide/reusable-animations)  
[Route transition animations](https://angular.io/guide/route-animations)
[@angular/animations API](https://angular.io/api/animations)


## Setup

1. Provide deps:
- In a standalone app, call `provideAnimations()` in the `providers` of `bootstrapApplication(...)`
- In an `NgModule` app, import `BrowserAnimations` module to the root `@NgModule`

2. In the component class, configure animations via calling `trigger(...)` inside `@Component.animations` array:
```
@Component({
  ...
  animations: [
    trigger('triggerName', [
      // states and transitions...
    ]),
  ],
})
```

3. In the HTML template, attach the trigger using `[@triggerName]` syntax:
```
// Example 1: bind a string that represents one of the explicit states
<some-element
  [@triggerName]="isOpen ? 'open' : 'closed'"
></some-element>

// Example 2: bind a boolean that represents the namesake true/false states, respectively
<some-element
  [@triggerName]="isOpen"
></some-element>

// Example 3: bind a number value
// (usually used for animating collection items)
<ng-container [@triggerName]="items.length">
  <some-element
    *ngFor="
      let item of items; 
      trackBy: ...
    "
  ></some-element>
</ng-container>

// Example 4: plain attribute without binding
// (if no custom states are used in the trigger)
<some-element
  @triggerName
></some-element>
```


## Configuring animations

Within a `trigger(...)`, specify an **array** of states and/or transitions between them.


### States

Define named states by calling `state(...)` and `style(...)`, e.g.:
```
  state('stateName', style({
    // CSS styles declarations
  }))
```

**NB!** When a state is active within a component, its styles **persist** on the element, even if when animation ends.

**NB!** `style(...)` units:
- a number: will be considered as `px`
- a CSS string unit, e.g. `10px`, `2rem`, `50%`
- `'*'` wildcard means _any value_ computed at runtime


### Transitions

A transition is defined by calling `transition(...)` with `animation(...)` and/or `style(...)`, e.g.:
```
  transition(stateChangeExpression, steps)
```

**`stateChangeExpression`** string examples:
- `state1 => state2`: when `state1` is replaced by `state2`
- `state1 <=> state2`: whenever state changes between `state1` and `state2`, in any direction
- multiple state transitions can be specified, e.g. `on => off, off => void`
- `*` is a wildcard state, i.e. _any state_ (including `void`)
  - `* => *` is any change between 2 states
- `void` means absence of the element in the DOM
  - `void => *` (aliased as **`:enter`**) = an element enters a view
  - `* => void` (aliased as **`:leave`**) = an element leaves a view
- `:increment` / `:decrement` selectors are triggered whenever the **numeric** value bound to the trigger increases or decreases, _e.g. if the length of the rendered list changes_

**NB!** `:enter` / `:leave` is fired whenever an element is added to / removed from the view via:
- `*ngIf` or `*ngFor`
- `ViewContainerRef`

With `:enter` / `:leave`, there is no need to bind any value to the trigger in the template.

**`steps`** can be either 1 step or an _array_ of steps, where step is either of:
- a call to `style(...)`: usually used to specify a starting or ending point of the transition
- one or several calls to `animate(timings, styles)`


#### `animate(...)`
Params:
- `timings`: a number (in milliseconds) or a string in format `'duration [delay] [easing-fn]'`:
  - if a string, duration & delay units must be specified, e.g. `10ms`, `0.5s`
  - `easing-fn` can be either of [CSS easing fns](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function), e.g.:
    - `ease` and `ease-in-out`: start slowly, then accelerate sharply, end slowly
    - `ease-in`: start slowly and accelerate to the end
    - `ease-out`: start abruptly and slow down to the end
    - `cubic-bezier(p1_x, p1_y, p2_x, p2_y)`
    - `linear`
    - `step-start` / `step-end`
- `styles` (optional): sets styles for the _parent animation_:
  - can be a call to `styles(...)` or `keyframes(...)`
  - if omitted or `null`, uses the styles from **destination state** (useful for _completion_ animations)


#### Order of animations

Transitions are matched in the order they are specified.  
More specific transitions should come first.  
`* => *` should come last.  

Once a parent animation is triggered, it gets priority and all child animations **are blocked** by default.  
To enable child animations selectively:
- `query(...)` an inner element
- call `animateChild(...)` inside `query(...)`

**NB!** when animating collections generated by `*ngFor` and ordering might change, always pass a `TrackByFn` to `*ngFor` to ensure animations order is correct.

