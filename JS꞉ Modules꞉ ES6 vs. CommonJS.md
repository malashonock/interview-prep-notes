---
tags: [JS/modules]
title: 'JS: Modules: ES6 vs. CommonJS'
created: '2023-10-19T10:25:42.349Z'
modified: '2023-10-19T12:56:33.142Z'
---

# JS: Modules: ES6 vs. CommonJS

[CommonJS vs. ES modules in Node.js](https://blog.logrocket.com/commonjs-vs-es-modules-node-js/)


## Exports

[Understanding module.exports and exports in Node.js](https://www.sitepoint.com/understanding-module-exports-exports-node-js/)

Named exports:
```
// ES6
export const obj1 = { foo: bar };
export {
  name1,
  name2,
};

// CommonJS
(module.)exports.obj1 = { foo: bar };
(module.)exports = {
  name1,
  name2,
};
```

**Note**: In most cases, `exports` can be treated as a shortcut synonym of `module.exports`. The only caveat is when both variants are used in a single module, assignments to `exports` are **discarded**. 

Default export:
```
// ES6
export default () => console.log(...);

// CommonJS
(module.)exports = () => console.log(...);
```


## Imports

Named imports:
```
// ES6
import { obj1 } from 'es6-module';
import { name1, name2 } from 'es6-module';
import * as ESM from 'es6-module'; // Usage: ESM.name1, ESM.name2

// CommonJS
const { obj1 } = require('cjs-module');
const { name1, name2 } = require('cjs-module');
const CJM = require('cjs-module'); // Usage: CJM.name1, CJM.name2
```

Default import:
```
// ES6
import loggerFn from 'es6-module';

// CommonJS
const loggerFn = require('cjs-module');
```
