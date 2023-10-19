---
tags: [JS/ES6, JS/modules]
title: 'JS: Modules: Exports'
created: '2023-10-19T09:35:24.402Z'
modified: '2023-10-19T09:36:55.577Z'
---

# JS: Modules: Exports

[MDN reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)

Makes a value available to the consumers of the module via `import`.

What can be `export`ed:
- `var`, `let`, `const`
- functions
- classes

**NB!** Only ES6 modules can use `import`.

**NB!** Only **top-level** declarations can be exported, `export` can't be used inside a fn or a JS statement.


## Named exports

There can be **any** number of named exports in a module.

**NB!** You can `export` a declaration **before** it's defined &mdash; module exports are NOT subject to _temporal dead zone_ rules.

Exporting single declarations:
```
export let name1;
export const name1, name2;
export const name1 = 1;
export function functionName(...) { ... }
export const functionName = (...) => { ... };
export class ClassName { ... }
export const { a: foo, b: bar };
export const { name1, name2: aliasedName2 } = obj; // object destructuring 
export const [name1, name2] = arr; // array destructuring
```

Exporting a list of declarations:
```
// Declarations...
export {
  name1,
  name2 as aliasedName2,
  name3 as 'invalid-identifier-also-allowed'
};
```

**NB!** `export { ... }` is not a JS object, it's just a list, i.e. JS grammar structure.

Re-exporting from other modules:
```
export { name1, name2 } from 'another-module';
export { import1 as name1, import2 as name2 } from 'another-module';
export * from 'another-module'; // re-export entire list of imports
export * as nameSpace from 'another-module'; // wrap re-exported list in a namespace
```

**NB!** Re-exporting is roughly the same as using `import` + `export`, but re-exported declaration are NOT available in the re-exporting module.


## Default export

There can be only **one** default export per module.

**Any JS expression** can be default-exported.

Default export examples:
```
export default 1 + 1;
export default function functionName(...) { ... }
export default (...) => { ... };
export default class ClassName { ... }
export default class { ... }
```

Default export can be included in a list of exports by the name `default`:
```
export { name1 as default, name2 };
```

Re-exporting default exports:
```
export { default } from 'another-module';
export { default as aliasedName } from 'another-module';
```
