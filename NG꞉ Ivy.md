---
tags: [Angular/Ivy]
title: 'NG: Ivy'
created: '2023-08-29T06:41:13.378Z'
modified: '2023-10-04T13:36:21.549Z'
---

# NG: Ivy

[Alex Rickabaugh talk @AngularConnect 2018](https://www.youtube.com/watch?v=isb5Ef6yI48)
[Kara Erickson talk @AngularConnect 2019](https://www.youtube.com/watch?v=S0o-4yc2n-8)

NG 2 compilers history:
1. **Template Compiler** (v.2.0+): imperative, quick, but too verbose
2. **View Engine** (v4.0+): declarative, compiles to a data structure that gets interpreted at runtime
3. **Ivy** (available since v.8, default since v.9)


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
      - _Lazy-loading without the Router (see [Jason Aden talk @AngularConnect 2018, starting 17:22](https://www.youtube.com/watch?v=MMPl9wHzmS4))_
      - _Dynamic builds_
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

Ivy code is safe to ship to npm, because it follows the locality principle.  
E.g., for `@Input('publicName') privateName: any`, Ivy generates template fn instruction like `ng.elementProperty(1, 'publicName', ng.bind(ctx.value))` which relies on public API of the component.


### Technical overview

Moving parts:
- Compiler (_ngc_):
  - transpiles TS to JS
  - translates NG decorators
  - translates HTML templates to code
- Runtime: supports rendering & CD in the browser

The compiler generates definitions with Ivy instructions.  
The runtime executes Ivy instructions as the app is running.

NG decorators get compiled to respective **static fields** on the types that are decorated, e.g.:
- @Component --> _ComponentClass_.**ɵcmp**
- @Directive --> _DirectiveClass_.**ɵdir**
- @Injectable --> _ServiceClass_.**ɵprov**


#### Factory function

`SomeClass.ɵfac` property.

Responsible for creating instances of the class.

Injected class Ctor dependencies become calls to `ng.inject(...)` internal fn.  
Once the injector provides all requested deps, the Ctor is called as normal.


#### Component template function

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

Instructions are grouped into 2 camps:
1. Creation instructions: to instantiate c/d's and create the DOM (correspond to template fn's creation mode)
2. Update instructions: to run CD (correspond to template fn's update mode)

CD, essentially, is running the template fn's of (by default) all components in the component tree, recursively, in update mode (rf = 2).


#### Component directives

The list of all directives available to the component is defined in the `ComponentClass.ɵcmp.dependencies` array.

Each time an Ivy `element` create instruction is run, NG runs through the list of component directives and tries to match any of the directive's selectors against the tag of the element being created &mdash; if a match is found, the matching directive is instantiated.  
**That's how NG components are created.**


### App bootstrap

Stages:
1. **Module bootstrap**:
  - instantiating modules
  - setting up `ModuleInjector`s
2. **Component bootstrap**:
  - View creation:
    - instantiating c/d's
    - creating the DOM
  - Change detection:
    - checking binding values
    - updating DOM as needed

NG will start bootstrapping with whatever component is specified in `@NgModule.bootstrap` array of the module booststrapped in `platformBrowserDynamic().bootstrapModule(...)` call in `main.ts`.

Root setup:
1. Locate root element in the `index.html`, e.g. `<app-root></app-root>`, and match in with one of the boostrap component's `@Component.selector`s
2. Instantiate root component by calling its component factory (`AppComponent.ɵfac`)
3. Render root component by calling its template fn (`AppComponent.ɵcmp.template`) in Creation mode.
4. As the template fn is executed, all child c/d's are instantiated recursively, and thus the entire DOM is created



### LView & TView

#### LViews

An array structure called **LView** (_Logical View_) is created for every component template instance, **1 LView per 1 component instance**.

LView keeps track of references to:
- DOM elements
- Binding values
- Directive instances

#### TView

Shared data for all instances of a component are stored in an array called **TView** (_Template View_), **1 TView for _all_ component instances**.

TView has the same structure / size as the corresponding LViews, and keeps track of:
- TNodes (_template nodes_) &mdash; metadata about the respective elements of the LView
- Directive definitions against the respective directive instances of the Lview


#### Role in creation mode

Ivy creation instructions, in addition to creating DOM elements, append the created element to the LView  at the specified index. 

**Why:** later, when CD is run, to have quick access to the DOM elements being updated (rather than using native `querySelector` API).

**NB!** The same `element` create instruction is used for creating both native DOM elements and NG components' host elements (e.g. `<app-root></app-root>`).

As elements are created and appended to the LView, _directive matching_ occurs against the list of directives available to the component, and if any directive matches, it gets instantiated and also appended to the LView.

Directive matching is done only once per component, not for each component instance.

When a component is instantiated the 1st time, along with the creation of its instance's LView, NG also creates the TView:
- for any element on the LView that is an NG host element, the corresponding TNode is created at the same index in the TView
- this TNode keeps the index of the respective directive definition which is also stored down the same TView
- Next time this component is instantiated, no directive matching is needed &mdash; NG just follows the TNode to the directive definition for a given element and instantiates it


#### Role in update mode

`advance(n)` instructions just increase the current index (cursor) to LView/TView to the next binding that needs checking.

Current values (before checking changes) are stored in the LView in the section that follows after the indices of the created elements.

CD algorithm:
1. Advance through component bindings
2. Calculate new value of the binding
3. Compare it with the current value stored in the LView
4. If value changed:
  - update the DOM
  - update the LView's current binding section

Ivy has a different order of running CD:
- in Render Engine, there were 2 passes of the entire tree from top to bottom:
  - check nodes for directive inputs
  - check nodes for DOM attributes and text bindings
- in Ivy, it's done in 1 pass, in the order nodes appear on the component's template:
  - binding order is guaranteed b/w nodes
  - binding order is _not_ guaranteed within a node (for the sake of optimization, Ivy instructions are chainable)


#### Lifecycle hooks

- `ngOnChanges`, `ngOnInit` and `ngDoCheck` are run *node-by-node*
- `ngAfterViewInit` / `ngAfterViewChecked`, `ngAfterContentInit` / `ngAfterContentChecked` are run view-by-view

As template fn is being executed:
- each time `advance` is called, it flushes the applicable lifecycle hooks for the just checked node (if any)
- just before returning from the fn, all remaining node-by-node hooks are flushed

After running the template fn:
1. Embedded views are refreshed, i.e. `ng-template`s added through `*ngIf`, `*ngFor`, etc.
2. Content hooks are flushed
3. Host bindings are set (?)
4. Child components are checked
5. View hooks are flushed
