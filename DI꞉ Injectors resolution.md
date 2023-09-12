---
tags: [Angular/DI]
title: 'DI: Injectors resolution'
created: '2023-09-12T09:02:35.005Z'
modified: '2023-09-12T09:12:53.097Z'
---

# DI: Injectors resolution

Injector = intermediary b/w deps providers and deps consumers.


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

Use case for `PlatformInjector`: providing services shared by _microfrontends_.


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
