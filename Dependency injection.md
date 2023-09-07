---
tags: [Angular/DI]
title: Dependency injection
created: '2023-09-05T11:51:47.994Z'
modified: '2023-09-07T07:31:44.551Z'
---

# Dependency injection

Usually, a dependency = a service.

Providing = registering a dep with an injector.  
Injecting = requesting a dep from NG DI system.  
Injector = intermediary b/w deps providers and deps consumers.

## Providing deps

### In injectable class definition

```
@Injectable({ providedIn: ... })
```

**NB!** This way of providing deps, unlike the other, is tree-shakeable!

#### `'root'`

Registers dep with the root injector.

Creates _a single instance_ of the dep that can be directly injected into _any_ class that needs it.

#### ~~NgModule (deprecated)~~

Registers dep with the `ModuleInjector` of the specified NgModule.

#### `null` or `undefined`

Dep is not provided automatically. Must be added to `providers` array of a `@NgModule`, `@Component` or `@Directive`.

#### `platform`

Registers dep with the platform injector.

#### ~~`any` (deprecated)~~

Provide one singleton instance of the dep across all eagerly-loaded modules, and create own instances for lazy-loaded modules.

### In `@NgModule.providers`

Dep will be available to:
- all d/c/p declared in this module
- all d/c/p declared in other modules within the same `ModuleInjector` applicable to this module

_The same instance_ of the dep is shared across all applicable consumers.


### In `@Component.providers` or `@Directive.providers`

Dep will be available to:
- all instances of this c/d
- other c/d's _used in the template of this component_

Each new instance of the component will receive _a new instance_ of dependency.

When a component instance is destroyed, so is the injected dep instance.

### In `@Component.viewProviders`

Dep will be available to consumers within the component `LView`.  
Dep will not be visible to content projected by the parent.


## Injecting deps

1. Inside a class **Ctor**
2. Using the global `inject()` fn


## Configuring deps

### Configuring class deps

`providers: [SomeServiceClass]` is shorthand for 
```
providers: [{ 
    provider: SomeServiceClass,
    useClass: SomeServiceClass
}]
```

#### `useClass`

Always creates _a new instance_ of the specified class via the `new` operator.

Can be used to override a default class with an extended alternative: 
`[{ provide: BaseClass, useClass: ExtendedBaseClass }]`

#### `useExisting`

Maps one DI token to another.
In effect adds a DI alias to a dep.
Creates several ways to access the same dep.

#### `useFactory`

**Use case:** when a dep resolution depends on some value known at runtime only.

**NB!** Factory fn deps must be specified in `deps` property.

```
const someServiceFactory = (authService: AuthService) => {
  return new SomeService(authService.isAuthenticated);
};

export const someServiceProvider: Provider = {
  provide: SomeService,
  useFactory: someServiceFactory,
  deps: [AuthService],
}
```

#### `useValue`

Associates a fixed value with a DI token.

Use cases:
- configs
- API urls
- mocks in unit tests

### Configuring non-class deps

Define a custom token:
`export const CUSTOM_TOKEN = InjectionToken<TValue>('token description');`

Register (provide) the token at some level:
`providers: [{ provide: CUSTOM_TOKEN, useValue: someValue }]`

Inject into consumer as needed:
`constructor(@Inject(CUSTOM_TOKEN) someValue: TValue) { }`

**NB!** TS interfaces can't be used as provider tokens (in `Provider.provide` prop)

**NB!** TS interfaces can't be injected as is, without `@Inject()`


## Injection context

- Ctor of a class instantiated by NG DI, e.g. `@Injectable` or `@Component`
- Factory fn used in `Provider.useFactory` or passed to `InjectionToken` Ctor
- In a fn run in injection context:
  - NG-defined, e.g. router's `CanActivateFn`
  - user-forced via `runInInjectionContext()` fn

**NB!** `inject()` fn can only be run in an injection context.


## Injectors hierarchy

There are 2 types of injectors:
- `ModuleInjector`
- `ElementInjector`


### Module injectors

`ModuleInjector`s are configured via `@NgModule` or `@Injectable` decorators.

Module injectors hierarchy:
1. **Null injector**: if DI reaches it, NG error is thrown
2. **Platform injector**: created by `platformBrowserDynamic()` fn (called in `main.ts`) as configured by `PlatformModule`
3. **root injector**: created by `bootstrapModule(AppModule)` fn (called in `main.ts`) as configured by `AppModule`
4. **child injectors**: (optionally) created during the instantiation of lazy-loaded modules

**NB!** Module injector is a _flattening_ of `@NgModule.providers` arrays of all the modules that can be reached via `@NgModule.imports`. Child injectors are only for lazy-loaded modules, which are not available when root injector is created.


### Element injectors

Created implicitly for every DOM element.  
Empty by default, unless configured via `@Component.providers` or `@Directive.providers`.


### Injector resolution rules

Precedence:
1. Check this component's `ElementInjector`
2. Look up parents' `ElementInjector`s chain
3. Check the `ModuleInjector` of the module where this component is declared
4. Look up parents' `ModuleInjector`s chain

Lookup stops once DI finds a provider, or if it reaches the `NullInjector`.


#### Resolution modifiers

`@Optional()`:
- if dep is not resolved, returns `null` instead of throwing an error

`@SkipSelf()`:
- ignore own `ElementInjector`, i.e. ignore `providers` and `viewProviders` of this component
- start lookup from this component's _parent_

`@Self()`:
- only look up in this c/d's `ElementInjector` (opposite of `@SkipSelf()`)
- no further walking up the tree
- _Use case:_ share dep instance between directives applied to the same element.

`@Host()`:
- start lookup from this c/d's `ElementInjector` (own `providers` and `viewProviders`)
- if dep is not resolved, look up dep in parent (host) component LView, i.e. in host component `viewProviders`
- no lookup in host component `providers`
- no further walking up the tree


#### LView hierarchy

[DI examples on Angular.io](https://angular.io/guide/hierarchical-dependency-injection#providing-services-in-component)
[Misko Hevery on Ivy data structures](https://blog.angular.io/ivys-internal-data-structures-f410509c7480)

To facilitate understanding, the following mental model can be helpful:

```
<root #LView> // created implicitly
  <app-root>
    <app-root's #LView>
      <parent> // ParentComponent
        <parent's #LView>
          <div>
            Projected content
            <ng-content></ng-content> // 2nd child's slot
          </div>          
        </parent's #LView>
        <!-- Notice projected child is outside parent's #LView -->
        <child> // 1st ChildComponent, content child
          <child's #LView>
          </child's #LView>
        </child>
      <parent>
      <child> // 2nd ChildComponent, view child
        <child's #LView>
          I am a child
        </child's #LView>
      </child>
    </app-root's #LView>
  </app-root>
</root #LView>
```

**NB!** Projected content is part of projector's LView, not part of the container component's LView.

Now, if we add DI providers and consumers:

```
<root #LView>
  <app-root // AppComponent
    @NgModule(AppModule) // AppModule (=root) injector
  >
    <app-root's #LView
      @Inject(SomeService) // Dep declared in AppComponent
    >
      <parent // ParentComponent
        @Provide(SomeService) // ParentComponent providers
      >
        <parent's #LView
          @Provide(SomeService) // ParentComponent viewProviders
        >
          <div>
            Projected content:
            <ng-content></ng-content> // 2nd child's slot
          </div>
        </parent's #LView>
        <!-- Notice projected child is outside parent's #LView -->
        <child> // 1st ChildComponent, content child
          <child's #LView
            @Inject(SomeService) // Dep declared in ChildComponent
          >
            I am a child
          </child's #LView>
        </child>
      <parent>
      <child> // 2nd ChildComponent, view child
        <child's #LView
          @Inject(SomeService) // Dep declared in ChildComponent
        >
          I am a child
        </child's #LView>
      </child>
    </app-root's #LView>
  <app-root>
</root LView>
```

