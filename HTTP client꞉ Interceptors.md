---
tags: [Angular/HTTP]
title: 'HTTP client: Interceptors'
created: '2023-09-12T10:30:53.350Z'
modified: '2023-09-12T14:12:01.303Z'
---

# HTTP client: Interceptors


## Overview

Interceptors are _middlewares_ through which flow all HTTP events related to an HTTP request to the backend, and the subsequent HTTP response form the backend:
```
HttpClient
  |     Λ
  V     |
Interceptor #1
  |     Λ
  V     |
Interceptor #2
  |     Λ
  V     |
...........
  |     Λ
  V     |
Interceptor #N
  |     Λ
  V     |
HttpBackend
===========
  |     Λ
  V     |
HTTP Server
```

From tech standpoint, an interceptor is an `@Injectable` service class that implements `HttpInterceptor` interface with 1 method, `intercept()` with the following signature:
```
type HttpInterceptorFn = (req: HttpRequest, next: HttpHandler) => Observable<HttpEvent>
```

where `HttpHandler` is an interface with 1 method, `handle()`, with the following signature:
```
type HttpHandlerFn = (req: HttpRequest) => Observable<HttpEvent>
```

`next` is the next interceptor in the chain of interceptors.  
The last handler in chain is `HttpBackend` which eventually sends the request to an external HTTP server.

**NB!** `HttpRequest` and `HttpResponse` events are largely _immutable_.
If an interceptor needs to modify any of them, a new instance should be created via `clone({ /* modified props */ })`.
Deep object immutability is not ensured and should be avoided, e.g. `req.body.someObjectProp` is still mutable.  

**Tip**: Set `req.body` to `null` to clear the request body.  
Setting it to `undefined` leaves it as is without modifying.


## Providing for DI

1. Decorate service class with `@Injectable`, optionally set `providedIn` prop
2. Provide the interceptor under `HTTP_INTERCEPTORS` token **in the same injector (module) that provides `HttpClient`** (or any parent injector above it)

```
{
  provide: HTTP_INTERCEPTORS,
  useClass: MyHttpInterceptorClass,
  multi: true,
}
```

**Tip**: Include all HTTP interceptors in a single array of providers and provide this barrel array.

**NB!** Interceptors handle the HTTP _request_ **in the order they were provided**.  
HTTP _response_ is handled in reverse order.


## Use cases

### Transform the outbound HTTP request

Pattern:
```
// Do something with the request
const modifiedReq = req.clone({
  ... // modified props
});

// then pass the modified request to the next handler
return next.handle(modifiedReq);
```

**Common examples**:
- Logging requests
- Transform/sanitize/standardize the request payload before sending to sender
- Append authorization headers
- Conditionally handle the request
  - _prepend different base URLs for development and production environments_

### Transform the inbound HTTP response

Pattern:
```
return next.handle(req)
  .pipe(
    // Add response transformations here
  )
```

**Common examples**:
- **Centralized error handling**
- Logging responses
- Transform/sanitize/standardize the response payload:
  - _convert date strings to Date objects_
  - _custom JSON parsing_
- Automatically retry failed requests


### Short-circuit the HTTP response

**Example**: Cache HTTP requests:
```
const cachedResponse = this.cache.get(req);
return cachedResponse
  ? of(cachedResponse)
  : next.handle(req).pipe(
      tap((event: HttpEvent): void => {
        if (event instanceof HttpResponse) {
          this.cache.set(req, event);
        }
      })
    )
```


### Full-cycle request interception

**Example**: Set loading flag to `true` when request starts and to `false` when it ends:
```
this.loading = true;
next.handle(req).pipe(
  finalize((): void => {
    this.loading = false;
  })
)
```

## Pass data to interceptors

`HttpRequest`s have a `context` prop that is available for all interceptors to read and modify throughout the lifetime of the request.  
This enables configuration of interceptors.

**Example**: With regard to the retry interceptor, we'd like to configure the number of retries per each request.

### Create HTTP context token

Create an instance of `HttpContextToken` class and pass the `defaultValue` fn to its constructor:
```
export const RETRY_COUNT = new HttpContextToken(() => 3);
export const ERROR_COUNT = new HttpContextToken(() => 0);
```

`defaultValue` fn is used for the following:
- for TypeScript to infer the type of the token
- to set its default value

### Set context when making a request

In a service making an HTTP request:
```
this.http.get(someUrl, {
  context: new HttpContext().set(RETRY_COUNT, 5),
})
```

**NB!** `HttpContext`'s `set()` and `delete()` methods are chainable.

### Use context in an interceptor

```
return next.handle(req).pipe(
  tap({
    error: (): void => {
      req.context.set(
        ERROR_COUNT, 
        req.context.get(ERROR_COUNT) + 1
      );
    }
  }),
  retry(req.context.get(RETRY_COUNT))
)
```

**NB!** Notice `req.context` is mutable via `set()` and `delete()` methods.


