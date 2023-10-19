---
tags: [JS/ES6, JS/modules]
title: 'JS: Modules: Imports'
created: '2023-10-19T09:36:12.203Z'
modified: '2023-10-19T09:50:07.375Z'
---

# JS: Modules: Imports


## Static `import` statement

[MDN reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)

**NB!** Only ES6 modules can use `import`.

**NB!** The importing module can NOT re-assign the imported bindings.

Named imports:
```
import { name } from 'another-module';
import { name1, name2 } from 'another-module';
import { name as aliasedName } from 'another-module';
import { 'invalid-name' as validName } from 'another-module';
```

Default import:
```
import defaultExportName from 'another-module';
import defaultExportName, { namedExportName1, namedExportName2 } from 'another-module'; // default import must come first
import { default as someAliasRequired } from 'another-module';
```

Namespace import:
```
import * as moduleName from 'another-module';
import defaultExportName, * as moduleName from 'another-module'; // default import must come first
// moduleName.default === defaultExportName
```

**NB!** Module namespace object is a **sealed JS object with `null` prototype**. Default export is available on it by the key `default`.

Side-effect-only imports:
```
import 'polyfills-module';
```

**NB!** `import` declarations are **hoisted**:
- you can use them **before** they are `import`ed
- side-effects are run **before** the rest of this module code is run


## Dynamic `import(...)` expression

Features:
- it's **not a JS function** &mdash; it's a built-in JS *keyword*
- loads a module **asynchronously**
- doesn't need to be top-level
- allows to import a module **at runtime**, e.g. conditionally or on demand (_lazily_)
- allows to import a module **in a non-module environment**, e.g. in a regular `<script>`
- allows to import a module that **does NOT exist or is unavailable** at load time
- allows to decide which module to import at runtime, depending on the environment (_isomorphic_ code)
- `moduleName` string can be constructed at runtime, e.g. you can loop over a list of modules

Dynamic `import(...)` returns a **promise** that resolves to a **module namespace object**, i.e. a sealed JS object containing all exports of the imported module (including `default`).

`import(...)` accepts a `moduleName` arg that is coerced to a string.


