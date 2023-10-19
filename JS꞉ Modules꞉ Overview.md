---
tags: [JS/ES6, JS/modules]
title: 'JS: Modules: Overview'
created: '2023-10-19T08:29:46.841Z'
modified: '2023-10-19T10:24:08.396Z'
---

# JS: Modules: Overview


## File extension: `.js` vs `.mjs`

[`V8` recommends](https://v8.dev/features/modules#mjs) to use `.mjs` extension for ES6 modules:
- to make it clear for a reader that a script is not a regular JS script, but an ES6 module
- to facilitate parsing by some runtimes (Node.js) and build tools (Babel)

**NB!** For the browser, file extension doesn't matter &mdash; what matters is its MIME type: it must be served as `text/javascript` by the server. `<script type="module">` tells the browser it's an ES6 module.

**NB!** `<script type="module">` behaves roughly the same as `<script defer>` &mdash; no need to apply `defer` attribute to ES6 modules.

Imported declarations are **live bindings**:
- they can NOT be re-assigned by the _importing_ module
- their values can be updated by the _exporting_ module


## Module name resolution

By default, browsers recognize:
- **absolute** paths
- **relative** paths, i.e. relative to the `wwwroot` (=where `index.html` is placed)
- defined file extension (`.js` or `.mjs`)

Some environments (like Node.js) allow:
- absolute-_looking_ paths which are not actually valid absolute paths
- omitting file extension

In browsers, the above tricks can be done using **import maps**.

**Import map** is a `<script type="importmap">` that contains a **JSON** object containing optional keys:
- `imports`: an object that defines general path mapping
- `scopes`: an object that defines _scoped_ path mappings for specific _importing_ modules

**`imports`** object structure:
- string keys are **path aliases**
- string values are valid module paths: absolute or relative to root, with file extensions
- failing the trailing `/` in a path alias, the **entire** string key is matched
- given a path alias ends with `/`, string key is matched as path **prefix**

**NB!** If multiple path aliases are matched, the browser picks the most specific (=the longest) path alias.

**NB!** Import- maps are only applied to `import`s (static and dynamic), NOT to `<script src="...">`.

**Tip**: Import maps can be used to solve the problem of **hashed filenames**.

**`scopes`** object structure is nested:
1. on the 1st level, keys are paths (usually absolute) to **importing** modules within which specific path mappings must apply
2. each scope is an object that has the same structure as `imports`


## Top-level `await`

ES6 modules can use `await` at top-level.

Features:
- code in such modules will be evaluated before it can be used by the importing modules
- script loading will not be blocked, however

