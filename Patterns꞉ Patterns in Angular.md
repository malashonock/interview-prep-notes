---
tags: [NG/patterns, Patterns]
title: 'Patterns: Patterns in Angular'
created: '2023-10-19T15:07:39.221Z'
modified: '2023-10-20T13:02:43.911Z'
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


## Factory Method

Under the hood, NG compiles its decorators into static methods on the decorated classes.

One of such methods is the factory method that embodies the component instantiation logic, including requesting all its dependencies from the DI system.

By so doing, component construction logic, that is defined by the dependencies specified by the application programmer, are separated from the core NG logic embedded in the framework.

Explicitly, we deal with component factories when creating components dynamically using the `ComponentFactoryResolver` and `ComponentFactory` APIs.


## Observer

NG uses RxJs heavily (e.g. in its `EventEmitter`), which is based on the Observer pattern.

RxJs `Subject` = Subject / Publisher in the Observer pattern: 
- `Subject` keeps track of its observers internally
- `Subject#next()` = `notifySubscribers(data)`
- `Subject#subscribe(subscriber)` allows to add a new subscriber
- `subscribe()` returns the unsubscription fn that can be used to remove the subscriber from the list of observers

RxJs `Observer` interface = `Subscriber` interface in the Observer pattern:
- `Observer#next(data)` = `update(data)`
- additionally, RxJs defines separate methods, `error()` and `compolete()`, for finer-grained communication


## Decorator

NG uses experimental TS decorators heavily, e.g.:
- `@NgModule`
- `@Component`, `@Directive`, `@Pipe`
- `@Injectable`, `@Inject`
- `@Input`, `@Output`

E.g. class decorators add internal static methods to the decorated class that are subsequently called by the framework.


