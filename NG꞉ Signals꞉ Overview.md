---
tags: [Angular/signals]
title: 'NG: Signals: Overview'
created: '2024-01-04T12:49:20.080Z'
modified: '2024-01-04T14:17:09.706Z'
---

# NG: Signals: Overview

[angular.dev](https://angular.dev/guide/signals)

**Signal** - a (reactive) wrapper around a value that notifies interested consumers whenever that value changes.

**NB!** Whenever a signal within an `OnPush` component changes its value, NG marks the component for change. 

2 types of signals:
- **writable**: read + write
- **computed**: read only

**Effect** - a task that runs whenever an underlying signal changes.


## Signal equality fn

To customize signal equality check algorithm, pass an equality fn as `equality` option to the signal creation fn (`signal()`/`computed()`).

Equality fn signature: `ValueEqualityFn<T>: (a: T, b: T) => boolean`


## Excluding tracking deps

To prevent an underlying signal read from being tracked by `computed()/effect()`, wrap a call to its getter into `untracked()`.

**NB!** `untracked()` can wrap a block of code - any signals read within this block won't be tracked.


