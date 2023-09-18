---
tags: [Angular/modules]
title: 'NgModules: Classification'
created: '2023-09-18T13:49:42.617Z'
modified: '2023-09-18T14:00:26.014Z'
---

# NgModules: Classification

## Domain/feature modules

Used to deliver a particular feature or app domain.

Features:
- top component is usually _the only exported component_ 
- other supporting components are usually private
- consists mostly declarables
- rarely have `providers` (if have, their lifetime is scoped to this NG module)


## Routed modules

The NG modules that are lazy-loaded via calls to `loadChildren()` in routing modules.

Features:
- should never export anything, 'cause their declarables never appear in external templates
- should never be imported in any other NG module - otherwise will be eagerly loaded
- rarely have `providers` (if have, their lifetime is scoped to this NG module)


## Routing modules

Used to configure routing in a separate module:
- define routes
- import and configure `Router` (`forRoot()` or `forChild()`)
- add guards and resolvers

Features:
- should not have declarations
- should re-export `RouterModule` to make `RouterLink` and `RouterOutlet` available to the main module
- usually, a routing module is created next to the main module, named `{main-module-name}-routing.module.ts`


## Service modules

Features:
- have only `providers`
- don't have any `declarations`
- are imported by the root module


## Widget modules

Features:
- should consist entirely of `declarations`
- most `declarations` are exported
- rarely have any `providers`


## Shared modules

Features:
- typically named `SharedModule`
- can be imported by any other module, even lazy-loaded
- contain commonly used c/d/p
- should not have `providers`
- imported modules should not have `providers`, either


## Comparative table

Criterion | Domain modules | Routed modules | Routing modules | Service modules | Widget modules | Shared modules
--- | --- | --- | --- | --- | --- | ---
Declarations | any | any | - | - | any | any
Exports | top component | - | RouterModule | - | most of the declarations | most of the declarations + module re-exports
Providers | - | - | - | any | - | -
Imports | any | any | RouterModule.forXXX() | any | any | any




