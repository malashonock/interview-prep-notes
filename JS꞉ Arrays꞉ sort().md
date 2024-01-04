---
tags: [JS/arrays]
title: 'JS: Arrays: sort()'
created: '2024-01-04T11:59:14.037Z'
modified: '2024-01-04T12:17:11.872Z'
---

# JS: Arrays: sort()

[MDN reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

Sorts elements **in place**.  
Returns reference to the same array, now sorted.


## Features

- `sort()` is stable since ES10 (ECMAScript 2019), before ES10 - stability was not guaranteed (_stable = keep original order for equal elements_)
- `undefined`s (or empty slots) are always moved to the **end** of array


## Default sorting algorithm

`sort()`

- Based on **toString()** conversion of elements
- **Ascending** (1 -> 9, A -> Z), i.e. lexicographically


## Custom compare fn

`sort(compareFn)`
`compareFn: (a, b) => number`

The sign of the returned number works as follows:
- if `a` should come **before** `b`, return a **negative** number
- if `a` should come **after** `b`, return a **positive** number
- if `a` **equals** `b`, return **zero** (=keep original order).


## (Im)mutability

`sort()` mutates the original array.

The immutable version is `toSorted()`, i.e. it returns a sorted **copy** of the original array.
