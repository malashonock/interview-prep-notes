---
tags: [Angular/animations]
title: 'Animations: Special tricks'
created: '2023-10-04T06:50:04.940Z'
modified: '2023-10-04T08:33:54.929Z'
---

# Animations: Special tricks


## `query(...)`

**Use case**:
 - place an animation trigger on a containing element
 - animate child elements selectively

Works similar to `element.querySelectorAll()`.

Params:
- `selector`:
  - `:self`: the animating element itself
  - `:enter` / `:leave`
  - `:animating`: all elements currently being animated
  - `@triggerName`: all elements with specified trigger applied thereto
    - `@*`: all elements with _any_ trigger applied
  - any other valid CSS selectors
  - **NB!** selectors can be combined, e.g. `div.record:enter`
- `steps` (as in `transition(...)`):
  - to apply animations directly, use `animate(...)`
  - to run chilren's animations, use `animateChildren(...)`
- `options` object (optional):
  - `limit`: cap the selected collection
  - `optional`:
    - `false` (default): throw an error if 0 elements are found
    - `true`: ignore silently if 0 elements are found

**NB!** `query(...)` can reach any elements within components using:
- `ViewEncapsulation.Emulated` (default)
- `ViewEncapsulation.None`

`ViewEncapsulation.ShadowDom` hides its elements from `query(...)`!


## Managing the order of animations


### `keyframes(...)`

Passed as `steps` arg in a call to `animate(...)`.

**Use case**: to create an animation that:
- consists of multiple steps
- steps must run in sequence

Accepts an array of `style(...)` calls that represent the animation steps.

By default, steps are distributed evenly.  
To fine-tune, specify an `offset` prop (0 through 1) inside each `style(...)`.

Example:
```
animate('2s', keyframes([
  style({ backgroundColor: 'blue', offset: 0}),
  style({ backgroundColor: 'red', offset: 0.8}),
  style({ backgroundColor: 'yellow', offset: 1.0})
])),
```


### Sequential animations: `sequence(...)`

Call `sequence(...)` and pass it an array of `steps` (as in `transition(...)`):
 - use `style(...)` to apply styles immediately
 - use `animate(...)` to apply over a given time span


### Delay sequential animations

1. `query(...)` child elements in a collection
2. within `query(...)` `steps` array, call **`stagger(...)`**

`stagger(...)` params:
- `delay`: a number in ms or a string
- `steps` (as in `transition(...)`)


### Parallel animations: `group(...)`

**Use case**: to animate different CSS properties independently.

Usage:
1. call **`group(...)`** as one of the `steps`
2. pass it an array of `steps` (as in `transition(...)`)


## Disable animations: `@.disabled`

Options:
- place special `[@.disabled]` trigger onto an element in the HTML template:
```
<div [@.disabled]>
  ...
</div>
```
- place a boolean `@HostBinding('@.disabled')` in a component class:
```
@HostBinding('@.disabled')
public animationsDisabled = false;
```

**NB!**: once animations are disabled on an element, any animations on its child elements will be disabled as well.

To enable selected child animations:
- `query(...)` an inner element
- call `animateChild(...)` inside `query(...)`

**Tip**: to disable _all_ animations in the app (_e.g. for e2e testing_), disable animations on the bootstrap component using `@HostBinding('@.disabled')`.


## Reusable animations


### Reuse animations

1. Define a reusable animation:
  - call `animation(...)`
  - pass it animation `steps` (as in `transition(...)`)
    - external params can be specified using the interpolation syntax, e.g.:
      - `opacity: {{ opacity }}`
  - assign the result to an exported variable
2. Reuse the animation: in any of the `steps` (in `transition(...)` etc.):
  - call `useAnimation(...)`
  - pass it the imported animation
  - optionally, pass an `options` object with the nested `params` object specifying external options, e.g.:
    - `{ params: { opacity: 0.7 } }`


### Reuse triggers

1. Define a reusable trigger:
  - call `trigger(...)`
  - assign the result to an exported variable
2. Reuse the trigger:
  - in any of `@Component.animations` array, include the imported trigger (as is)


## Animation callbacks

The animation `trigger(...)` emits events twice:
- when am animation starts: `(@triggerName.start)="..."`
- when an animation finishes: `(@triggerName.done)="..."`

The `$event: AnimationEvent` arg has the following props:
- `triggerName`
- `phaseName`: `start` | `done`
- `totalTime`: time (in ms) for the animation to complete
- `fromState` / `toState`
- `element`: the HTML element being animated



