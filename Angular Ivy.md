---
tags: [Angular/Ivy]
title: Angular Ivy
created: '2023-08-29T06:41:13.378Z'
modified: '2023-08-29T15:27:50.172Z'
---

# Angular Ivy

[Alex Rickabaugh talk @AngularConnect 2018](https://www.youtube.com/watch?v=isb5Ef6yI48)

NG 2 compilers history:
1. **Template Compiler** (v.2.0+): imperative, quick, but too verbose
2. **View Engine** (v4.0+): declarative, compiles to a data structure that gets interpreted at runtime
3. **Ivy** (available since v.8, default since v.9)

### Overview

Moving parts:
- Template compiler (_ngc_): translates HTML templates to code
- Runtime: supports rendering & CD in the browser

NG decorators get compiled to respective **static fields** on the types that are decorated, e.g.:
- @Component --> _ComponentClass_.**ɵcmp**
- @Directive --> _DirectiveClass_.**ɵdir**
- @Injectable --> _ServiceClass_.**ɵprov**

### Factory function

`SomeClass.ɵfac` property.

Responsible for creating instances of the class.

Injected class Ctor dependencies become calls to `ng.inject(...)` internal fn. Once the injector provides all requested deps, the Ctor is called as normal.

### Component template function

`@Component.template` gets compiled to `ComponentClass.ɵcmp.template` property &mdash; the **template function**

Its code is imperative, like Template Compiler.

Template fn is passed 2 args:
- `rf` is _render flag_ (1 = create mode, 2 = update mode)
- `ctx` is _context_, i.e. the **component instance**

Contains 2 `if` blocks:
1. `(rf & 1)` Renders the component the 1st time it's inserted into the DOM
2. `(rf & 2)` Runs CD

Instruction set (IS) = individual rendering fns  that are output by ngc ($\approx$ assembly language for NG runtime).

Rationale for breaking down into smaller fns &mdash; to enable tree-shaking: those instructions that are not used will be left out.

Instructions are grouped into 2 sequences:
1. to create and insert
2. to run CD

### Ivy benefits

1. Optimizability
   - Tree-shaking (_aka dead code elimination_)
2. Incrementality
   - Speed up compilation by AOT compiling imported libs, `ng build` only builds the app
   - Locality principle: publishing relies on stable public APIs only
   - Less strict requirements for lib publishers
3. Flexibility
   - Ivy project is a flexible foundation going forward
   - Instruction set is extensible: new features = new instructions + old ones still working
   - _Example forthcoming features:_
      - _Hybrid JIT+AOT apps_
      - _Lazy-loading without the Router_
      - _Higher-order components_
      - _Templates written in Ivy IS rather than in HTML_

#### Tree-shaking

How it works:
1. Don't use sth in the app?
2. Ngc doesn't generate those instructions
3. Unused framework features are left out of the app bundle. 

Common sources of tree-shakeable code:
- NG itself
- 3rd party libraries.

Pretty much _any_ NG feature can be tree-shaken, even DI!

View Engine was fundamentally not tree-shakeable, because it interprets generated ngFactories at runtime --> so it must support _every possible_ NG feature.

Own factory functions for each component/directive.

Uses modern JS optimizers (_and is future-ready, too_).

#### Incremental compilation

View Engine used _global_ compilation, i.e. is had to compile all components from all imported libs during compilation.

Ivy uses _incremental_ compilation: libs (_e.g. NG Material_) are AOT compiled before publishing to npm.

Ivy code is safe to ship to npm, because it follows the locality principle. E.g., for `@Input('publicName') privateName: any`, Ivy generates template fn instruction like `ng.elementProperty(1, 'publicName', ng.bind(ctx.value))` which relies on public API of the component.


