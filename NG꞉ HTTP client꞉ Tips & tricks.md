---
tags: [Angular/HTTP, RxJs]
title: 'NG: HTTP client: Tips & tricks'
created: '2023-10-16T09:55:18.725Z'
modified: '2023-10-16T10:33:05.965Z'
---

# NG: HTTP client: Tips & tricks


## Avoid duplicate HTTP requests

[RxJS `shareReplay()`](https://rxjs.dev/api/operators/shareReplay)
[Tutorial](https://blog.bitsrc.io/avoiding-angular-duplicate-http-requests-with-the-rxjs-sharereplay-operator-773ba1b4ad5e)

Use RxJs `shareReplay()` operator factory:
- in a data service:
```
private data$: Observable<...> = this.httpClient<...>
  .get('some/url')
  .pipe(
    shareReplay(1), // Cache and share last emitted value
  );

public fetchData(): void {
  this.data$.subscribe(data => {
    // Handle the response
  });
}
```
- in a consuming component:
```
this.dataService.fetchData();
```


## Repeat and retry HTTP requests

[Tutorial](https://netbasal.com/repeat-and-retry-http-requests-using-rxjs-423c1996fb65)


### Repeat completed HTTP requests

[RxJs `repeat()`](https://rxjs.dev/api/operators/repeat)

Use RxJs `repeat()` operator.

**NB!** `repeat()` works only after the source observable **completes**.

**NB!** `repeat()` will NOT catch errors! Use `retry()` for that.

Examples:
```
repeat(); // repeat forever, without delay
repeat({ delay: 1000 }); // repeat forever with a 1s delay between repetitions
repeat({ count: 2, delay: 1000 }); // repeat 2 times with a 1s delay between repetitions

// Repeat HTTP request with a delay that increases by 1s
// until status is "completed"
this.httpClient.post('some/url', data)
  .pipe(
    repeat({
      delay: (count) => timer(count * 1000),
    }),
    filter((res) => res.status === 'completed'),
    take(1),
  );
```

**Note**: if `delay` is a fn:
- `count` arg = the number of the current repetition
- a _notifier observable_ must be returned:
   - when the notifier observable emits, `repeat()` will resubscribe to the source observable
   - when it completes _without emitting_, `repeat()` will complete without error
   - when it _throws an error_, the error will be pushed to the result


### Retry failed HTTP requests

[RxJs `retry()`](https://rxjs.dev/api/operators/retry)

Use RxJs `retry()` operator.

**NB!** `retry()` works only after the source observable **throws an error**.

Use a similar options object (with `count` and `delay` props + `resetOnSuccess`) as in `repeat()`.

Examples:
```
// Retry 3 times,
// with exponential backoff
// maxing out at 1 minute
retry({ 
  count: 3,
  delay: (count) => {
    return timer(Math.min(60_000, 2 ^ count * 1000));
  }
});
```
