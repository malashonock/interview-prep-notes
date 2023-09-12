---
tags: [Angular/router]
title: 'Router: Events'
created: '2023-09-12T08:37:04.950Z'
modified: '2023-09-12T08:50:17.629Z'
---

# Router: Events

[Router events description](https://angular.io/guide/router-reference#router-events)

Order or router events:
```
NavigationStart
|
| RouteConfigLoadStart + RouteConfigLoadEnd
|
| RoutesRecognized
|
| GuardsCheckStart
| |
| | ChildActivationStart
| | |
| | | ActivationStart
| V | |
| GuardsCheckEnd
|   | | 
|   | | ResolveStart + ResolveEnd
|   V |
|   ChildActivationEnd
|     V
V     ActivationEnd
NavigationEnd
NavigationCancel
NavigationError
Scroll

```
