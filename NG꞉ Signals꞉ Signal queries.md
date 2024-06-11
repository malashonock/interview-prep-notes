---
tags: [Angular/queries, Angular/signals]
title: 'NG: Signals: Signal queries'
created: '2024-06-11T06:17:17.269Z'
modified: '2024-06-11T06:59:05.691Z'
---

# NG: Signals: Signal queries

Return read-only signals (`Signal`) wrapped around the targeted values.

**NB!** Signal queries can only be used in c/d class field initializers:
```
export class AppComponent {
  public el: Signal<ElementRef | undefined> = viewChild('el'); // OK
  public cmp: Signal<MyComponent | undefined>;

  public constructor() {
    this.cmp = viewChild(MyComponent); // also OK
  }
}
```


## View queries

View queries retrieve results from the elements in component's own template (view).


### `viewChild`

Signal-based analog of `@ViewChild` to target a single result.

Query by string TRV:
```
// In component template:
<div #el></div>

// In component class:
divEl: Signal<ElementRef | undefined> = viewChild<ElementRef>('el');
```

Query by class:
```
// In component template:
<my-component />

// In component class:
myCmp: Signal<MyComponent | undefined> = viewChild(MyComponent);
```


### `viewChildren`

Signal-based analog of `@ViewChildren` to target multiple results.

```
// In component template
<div #el></div>
@if (show) {
  <div #el></div>
}

// In component class
show = true;
divEls: Signal<ReadonlyArray<ElementRef>> = viewChildren<ElementRef>('el');
```


### View query options

The only option available in signal queries is `read`: the type of result to inject from the matched nodes:
```
// In component template
<my-component />

// In component class
cmpEl: Signal<ElementRef | undefined> = viewChild(MyComponent, { read: ElementRef });
```


## Content queries

Content queries retrieve results from the component's projected content, i.e. elements nested inside the component's tag in the template where it is used.


### `contentChild`

Signal-based analog of `@ContentChild` to target a single result.

Query by string TRV:
```
// In component class:
divEl: Signal<ElementRef | undefined> = contentChild<ElementRef>('el');
```

Query by class:
```
// In component class:
myCmp: Signal<MyComponent | undefined> = contentChild(MyComponent);
```


### `contentChildren`

Signal-based analog of `@ContentChildren` to target multiple results.

```
// In component class
divEls: Signal<ReadonlyArray<ElementRef>> = contentChildren<ElementRef>('el');
```


### Content query options

Signal-based content queries support 2 options:
- `read`: the type of result to inject from the matched nodes
- `descendants`:
  - `false` (default): find only direct children
  - `true`: traverse all descendants of the component; however, queries do NOT descend into components


## Required single-child queries

If `viewChild` / `contentChild` don't find a result, they return `undefined`.

If a child query always reliably returns a result (e.g. by using its value in `ngAfterViewInit` / `ngAfterContentInit`), one can use the `required` version of the query to remove `undefined` from type signature:
```
// In component template
<div #requiredEl></div>

// In component class
existingEl: Signal<ElementRef> = viewChild.required('requiredEl');

ngAfterViewInit(): void {
  console.log(this.existingEl());
}
```

**NB!** If a `required` query didn't find a result _yet_ (either if there is no matching target, or if the query result was accessed too early), NG throws a **runtime error**.


## Signal-based vs. decorator-based queries

Benefits of signal queries:
- signal query results can be composed with other signals (using `computed` or `effect`)
- signal query results trigger CD automatically
- more predictable timing of results availability
- simpler API surface
- improved type safety, more accurate type inference
- lazier updates: NG doesn't execute query until some code explicitly reads the respective signal

