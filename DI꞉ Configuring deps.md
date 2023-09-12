---
tags: [Angular/DI]
title: 'DI: Configuring deps'
created: '2023-09-12T09:04:30.122Z'
modified: '2023-09-12T09:05:58.172Z'
---

# DI: Configuring deps


## Configuring class deps

`providers: [SomeServiceClass]` is shorthand for 
```
providers: [{ 
    provider: SomeServiceClass,
    useClass: SomeServiceClass
}]
```

### `useClass`

Always creates _a new instance_ of the specified class via the `new` operator.

Can be used to override a default class with an extended alternative: 
`[{ provide: BaseClass, useClass: ExtendedBaseClass }]`

### `useExisting`

Maps one DI token to another.
In effect adds a DI alias to a dep.
Creates several ways to access the same dep.

### `useFactory`

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

### `useValue`

Associates a fixed value with a DI token.

Use cases:
- configs
- API urls
- mocks in unit tests


## Configuring non-class deps

Define a custom token:
`export const CUSTOM_TOKEN = InjectionToken<TValue>('token description');`

Register (provide) the token at some level:
`providers: [{ provide: CUSTOM_TOKEN, useValue: someValue }]`

Inject into consumer as needed:
`constructor(@Inject(CUSTOM_TOKEN) someValue: TValue) { }`

**NB!** TS interfaces can't be used as provider tokens (in `Provider.provide` prop)

**NB!** TS interfaces can't be injected as is, without `@Inject()`
