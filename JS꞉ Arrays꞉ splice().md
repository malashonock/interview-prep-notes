---
tags: [JS/arrays]
title: 'JS: Arrays: splice()'
created: '2024-01-04T12:17:37.162Z'
modified: '2024-01-04T12:31:26.889Z'
---

# JS: Arrays: splice()

[MDN reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)

Can remove, replace and/or add new elements to an array.
Modifies the contents of array **in place**.  
Returns reference to the same array, now modified.


## Usage

Arguments:
1. `start`: 0-based index at which to start modifying the array
  - inclusive  
  - if omitted or too large (`start >= array.length`), no elements will be deleted
  - negative = count from the end of array (`start + array.length`)
  - too negative (`start + array.length < 0`) is treated as `0`
  - explicit `undefined` is treated as `0`
2. `deleteCount`: the number of elements to remove
  - if omitted or too large, just removes all elements from `start` to the end of array
  - if too small (`deleteCount <= 0`), no elements are removed
3. `...itemsToAdd`: elements to add, beginning from `start`


## (Im)mutability

`splice()` mutates the original array.

The immutable version is `toSpliced()`, i.e. it returns a modified **copy** of the original array.
