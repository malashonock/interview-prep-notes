---
tags: [Angular/Nx]
title: 'Nx: Nested remotes'
created: '2023-10-02T06:09:46.710Z'
modified: '2023-10-02T06:15:39.970Z'
---

# Nx: Nested remotes

To make a remote host other remotes (i.e. make a remote a nested shell):
1. Define _all_ remotes' URLs (including nested ones) in one `module-federation.manifest.json` of the **shell** app
2. In the nested shell's `remoteEntryRoutes` config, prepend `loadRemoteModule()` with a call to `setRemoteDefinitions()`:
```
  ...
  loadChildren: () =>
    fetch('/assets/module-federation.manifest.json')
      .then((res) => res.json())
      .then((definitions) => setRemoteDefinitions(definitions))
      .then(() => loadRemoteModule('admin', './Module'))
      .then((m) => m.RemoteEntryModule),
  ...
```

**NB!** In the root shell app, `setRemoteDefinitions` is called in `main.ts` &mdash; however, `main.ts` is not called by remotes, including the nested shell!
