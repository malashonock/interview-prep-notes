---
tags: [Angular/pipes]
title: 'NG: Pipes'
created: '2023-09-21T09:40:25.089Z'
modified: '2024-03-27T12:39:57.667Z'
---

# NG: Pipes

[Transforming Data Using Pipes](https://angular.io/guide/pipes)

A pipe is a function that:
- is used in the template
- accepts some input value
- returns a transformed value
- the returned value can be an input to the next pipe (=pipeable)

From tech standpoint, a pipe is a class that:
- is decorated with `@Pipe` decorator
- implements `PipeTransform` interface


## Using pipes in the template

```
{{ inputExpr | pipeName : pipeArg1 : pipeArg2 }}
```

Pipes can be chained.


## Pure/impure pipes

**Pure** pipes: `transform()` is invoked only when pipe input changes (resembles OnPush logic), i.e.:
  - if primitive input changes its value
  - if object input changes its **reference**

**NB!**: Mutating object inputs will NOT re-run a pure pipe.

**Impure** pipes are invoked at **each CD cycle**.

**NB!**: Impure pipes run frequently, so they should not be long-running.


## Built-in pipes

To use, import `CommonModule`.

Most built-in pipes are **pure**, except:
- `async` pipe
- `json` pipe
- `keyvalue` pipe
- `slice` pipe


### Date transformation


#### [`date`](https://angular.io/api/common/DatePipe) pipe

```
{{ inputExpr | date : format : timezone : locale }}
```

**Input type**: `Date` | `string` | `number`

Params (all optional):
1. `format`:
  - either string name of a pre-defined format (e.g. `'longDate'`)
  - or custom format string (e.g. `'MMM dd, yyyy'`)
2. `timezone`: a string time zone offset from GMT (e.g. `'+0430'`)
3. `locale` (only `en-US` comes out of the box)


### Text transformation

- [`uppercase`](https://angular.io/api/common/UpperCasePipe) pipe
- [`lowercase`](https://angular.io/api/common/LowerCasePipe) pipe
- [`titlecase`](https://angular.io/api/common/TitleCasePipe) pipe:
  - first letters of each word are uppercased
  - subsequent letters of each word are lowercased
  - words boundaries are defined by whitespaces
  
```
{{ inputExpr | uppercase }}
{{ inputExpr | lowercase }}
{{ inputExpr | titlecase }}
```

**Input type** everywhere: `string`

All above pipes don't accept params.


### Number transformation


#### [`number`](https://angular.io/api/common/DecimalPipe) pipe

**Aside**: Class is named `DecimalPipe`.

```
{{ inputExpr | number : digitsInfo : locale }}
```

**Input type**: `string` | `number`

Params:
1. `digitsInfo`: string pattern `{minIntDigits}.{minFracDigits}-{maxFracDigits}` (defaults to **`1.0-3`**)
2. `locale`: string (defaults to `en-US`)


#### [`currency`](https://angular.io/api/common/DecimalPipe) pipe

```
{{ inputExpr | currency : currencyCode : display : digitsInfo : locale }}
```

**Input type**: `string` | `number`

Params:
1. `currencyCode`: string (defaults to the currency of the current locale)
2. `display`: format of the currency indicator:
  - `'code'`: as ISO code (e.g. `CAD`, `USD`)
  - `'symbol'` (**=default**): as symbol (e.g. `CA$`, `$`)
  - `'symbol-narrow'`: as shortened symbol (e.g. `$`, `$`)
  - string: override with the given string (`''` effectively removes currency symbol)
  - ~~boolean (deprecated)~~:
    - `true` = `symbol`
    - `false` = `code`
3. `digitsInfo`: same as in `number` pipe, but defaults to **`1.2-2`**
4. `locale`: string (defaults to `en-US`)


#### [`percent`](https://angular.io/api/common/PercentPipe) pipe

Reprecents a number as percentage, i.e. **multiplied by 100** and with **`%`** symbol appended.

```
{{ inputExpr | percent : digitsInfo : locale }}
```

**Input type**: `string` | `number`

Params:
1. `digitsInfo`: same as in `number` pipe, but defaults to **`1.0-0`**
2. `locale`: string (defaults to `en-US`)


### [`async`](https://angular.io/api/common/AsyncPipe) pipe

```
{{ inputExpr | async }}
```

**Input type**: `Observable` | `Promise`

Does the following:
- subscribes to the input stream
- returns the latest value of the input stream
- whenever the input stream emits a new value, the pipe calls **`markForCheck()`**, thus triggering CD
- when the component is destroyed, **unsubscribes from source stream** automatically
- when the entire input _reference_ changes:
  - unsubscribes from the old input
  - subscribes to the new input

**NB!**: `async` pipe is **impure**.


### [`json`](https://angular.io/api/common/JsonPipe) pipe

Returns JSON-serialized representation of the input.

```
{{ inputExpr | json }}
```

**Input type**: any

**NB!**: `json` pipe is **impure**.


### [`keyvalue`](https://angular.io/api/common/KeyValuePipe)

Transforms an object or `Map` into an array of key-value pairs.

```
{{ inputExpr | keyvalue : compareFn }}
```

**Input type**: an object or `Map` with string or number keys

Params:
- `compareFn`: 
  - fn signature is `(a: KeyValuePair, b: KeyValuePair) => number`
  - sorting logic resembles that of `Array#sort()`:
    - if fn return is negative (`a - b < 0`), `a` comes before `b`
    - if fn return is positive, `b` comes before `a`
  - `defaultComparator` behaves as follows:
    - `null`s and `undefined`s are moved to the end
    - strings are compared lexicographically
    - numbers are compared in ascending order
    - for booleans, `false` comes first, `true` comes to the end
    - args of different types are stringified and compared as strings (lexicographically)

**NB!**: `keyvalue` pipe is **impure**.


### [`slice`](https://angular.io/api/common/SlicePipe)

```
{{ inputExpr | slice : start : end }}
```

**Input type**: an string or array

For **string** inputs, the pipe returns a substring.

For **array** inputs, the pipe returns a **new array** containing the slice of the input array.

Pipe behaviour is the same as of `Array#slice()` and `String#slice()`:
  - `end` is exclusive
  - negative indices are taken as `index` + `length` (adjusted)
  - `start`:
    - is included
    - **is required!**
    - if >= `length`, nothing is extracted
    - if (adjusted) < 0, copies from the very start
  - `end`:
    - is **excluded**
    - optional, defaults to `length`
    - if >= `length`, copies up to the very end (=`length`)
    - if (adjusted) < 0, nothing is extracted


## Custom pipes

To create a custom pipe:
1. Define a class
2. Decorate the class with `@Pipe` decorator:
  - `name`: _lowerCamelCase_ name that will be used in the template
  - (optional) `pure`: boolean, defaults to `true`
  - (optional) `standalone`: boolean
3. Implement the `PipeTransform` interface:
  - implement the `transform()` method:
    - type the input
    - type the return value
    - params can be specified as in regular JS fns

**Tip**: If a pipe relies on internal state, other than its inputs, it should be impure.

**Use case**: A good candidate for an impure pipe is an **HTTP fetch caching** pipe (OR implement caching via a custom _HTTP interceptor_)

