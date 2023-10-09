---
tags: [Angular/bindings, RxJs]
title: 'NG: EventEmitter vs. Subject'
created: '2023-10-09T13:28:25.826Z'
modified: '2023-10-09T13:35:30.602Z'
---

# NG: EventEmitter vs. Subject

NG `EventEmitter` extends `Subject`:
- `EventEmitter#emit(...)` method calls `super.next(...)` under the hood
- `isAsync` Ctor arg, when set to `true`, schedules event emission on next tick (via `setTimeout()`)

**NB!** `EventEmitter` does NOT automatically complete when the component is destroyed.

**Note**: NG team stated that `EventEmitter` should not be used for anythind except `@Output()`, i.e. should not be `pipe(...)`d or `subscribe(...)`d (although this recommendation is old and the implementation hasn't changed ever since).
