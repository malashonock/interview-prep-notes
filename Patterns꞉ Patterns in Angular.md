---
tags: [NG/patterns, Patterns]
title: 'Patterns: Patterns in Angular'
created: '2023-10-19T15:07:39.221Z'
modified: '2023-10-19T15:25:35.512Z'
---

# Patterns: Patterns in Angular


## Inversion of Control

NG is a framework that manages the control flow of a frontend application.

A frontend developer writes application code (components, directives, services, etc.) which doesn't make a cohesive program in itself &mdash; rather, all of these are pieces of code that are used by the NG framework at the right time.

We don't call Angular code &mdash; Angular calls our code:
1. We write some classes decorated with NG decorators.
2. At build time, this code is compiled to Ivy instructions executable by NG runtime.
3. At runtime, when NG renders the view tree, it instantiates components' classes by calling their constructors and executing their Ivy instructions as part of change detection.


## Dependency Injection

NG uses _Ctor injection_.

All Ctor args (=class dependencies) declared on NG-decorated classes, after AOT compilation, are wrapped in calls to an internal `inject(...)` method.

`inject(...)` is a request to the NG DI container (referred to as the _injector_ in NG) to return an instance of some dependency.

DI containers (injectors) are setup at various levels:
- environment/module injectors: when `@NgModule`s are instantiated
- node/element injectors: based on `providers` declared in `@Directive`s or `@Component`s

Dependency registration in NG is called `providing` dependencies, e.g.:
- in `providers` of an `@NgModule`, `@Directive` or `@Component`
- in `provideIn` of an `@Injectable`

