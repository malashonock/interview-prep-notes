---
tags: [Angular/DI]
title: 'NG: DI: Injectors resolution'
created: '2023-09-12T09:02:35.005Z'
modified: '2024-03-17T22:29:59.911Z'
---

# NG: DI: Injectors resolution


## Precedence of injector hierarchies

1. Check this component's element injector
2. Look up parents' element injectors chain
3. Check the environment injector of the module where this component is declared / root injector
4. Look up the environment injectors chain

`ChainedInjector` is the class responsible for switching from element injector hierarchy to environment injector hierarchy, once the element injector hierarchy has reached its top.


## Precedence of element injectors resolution

(Given no resolution modifiers)

Terms:
- _Self_ &mdash; the same component that requests a dep; for directives &mdash; the component that matches the `selector` of the directive that requests a dep
- _View parent_ &mdash; the component whose template references the component that requests a dep
- _Container (component)_ &mdash; the component into which the component that requests a dep was projected
- _(Content) projector_ &mdash; the component that projected the component that requests a dep into the container

Order of element injectors resolution:  

Order | Provider source | Provider type | Applicable requestors | @Self | @SkipSelf | @Host
:--- | :--- | :---: | :---: | :---: | :---: | :---: 
1\. Lookup on **self** |  |  |  |  |  |
1.1. | Self cmp | **view**Providers | cmp only **(n/a to dirs!)** | @Self start => |  | @Host start =>
1.2. | Self cmp **dirs** | providers | cmp+dir | ?&darr; |  | ?&darr;
1.3. | Self cmp | providers | cmp+dir | <= @Self end |  | ?&darr;
2\. Lookup upstream |  |  |  |  |  | 
2.a. Self is a **ViewChild**? |  |  |  |  |  | 
2.a.1. | View parent cmp | **view**Providers | cmp+**dir(!)** |  | @SkipSelf start => | <= @Host end
2.a.2. | View parent cmp **dirs** | providers | cmp+dir |  | ?&darr; | 
2.a.3. | View parent cmp | providers | cmp+dir |  | ?&darr; | 
2.b. Self is a **ContentChild**? |  |  |  |  |  | 
2.b.1.1. | Container cmp **dirs** | providers | cmp+dir |  | @SkipSelf start => | ?&darr;
2.b.1.2. | Container cmp | (!)providers | cmp+dir |  | ?&darr; | ?&darr;
2.b.2.1. | Projector cmp | **view**Providers | cmp+dir |  | ?&darr; | <= @Host end
2.b.2.2. | Projector cmp **dirs** | providers | cmp+dir |  | ?&darr; | 
2.b.2.3. | Projector cmp | providers | cmp+dir |  | ?&darr; | 


Lookup stops once DI finds a provider, or if it reaches the `NullInjector`.


#### Resolution modifiers

`@Optional()`:
- if dep is not resolved, returns `null` instead of throwing an error

`@SkipSelf()`:
- ignore own `NodeInjector`, i.e. ignore `providers` and `viewProviders` of this component and its directives
- start lookup from this component's _parent_

`@Self()`:
- only look up in `providers` and `viewProviders` of this component or its directives (opposite of `@SkipSelf()`)
- no further walking up the tree
- _Use case:_ share dep instance between directives applied to the same element.

`@Host()`:
- start lookup from this component's `NodeInjector` (own `providers` and `viewProviders`)
- continue lookup in the container's `providers`
- finish lookup in the view parent's / projector's `viewProviders`
- no further lookup in view parent's / projector's component `providers`
- no further walking up the tree


#### LView hierarchy

[DI examples on Angular.io](https://angular.io/guide/hierarchical-dependency-injection#providing-services-in-component)  
[Misko Hevery on Ivy data structures](https://blog.angular.io/ivys-internal-data-structures-f410509c7480)  
[Angular.dev example](https://angular.dev/guide/di/hierarchical-dependency-injection)  

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
