---
tags: [Angular/Ivy]
title: 'NG: JIT vs AOT'
created: '2023-10-16T12:15:36.416Z'
modified: '2023-10-16T12:40:53.501Z'
---

# NG: JIT vs AOT

[Angular docs](https://angular.io/guide/aot-compiler)
[SO thread #1](https://stackoverflow.com/questions/41450226/just-in-time-jit-vs-ahead-of-time-aot-compilation-in-angular)
[SO thread #2](https://stackoverflow.com/questions/50111036/angular-ngc-or-tsc)

There are 2 compilers involved:
1. `tsc` (TS compiler): compiles TS to JS
2. `ngc` (NG compiler): compiles, amongst other things, HTML templates into definitions containing Ivy instructions, runnable by the browser.

NG JIT compiler only performs TS compilation with `tsc`. `ngc` compiler is shipped as part of the bundle and performs the templates compilation at runtime.

NG AOT compiler performs both stages of compilation (`tsc` + `ngc`) during the build phase.

AOT advantages:
- **faster rendering**: the browser downloads the readily executable code (Ivy instructions), without waiting to compile it first
- **smaller bundle size**: `ngc` compiler works during the build phase and is not included in the bundle (which saves up to _half_ of the overall NG size)
- **fewer HTTP requests**: `ngc` inlines external HTML templates and CSS stylesheets within app JS, eliminating the need to request these assets at runtime
- **better security**: by inlining HTML and CSS, less network roundabouts are made, hence fewer opportunities for injection attacks
- **static template error checking**: `ngc` detects and reports template errors during the build step
