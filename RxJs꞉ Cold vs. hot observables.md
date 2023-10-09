---
tags: [RxJs]
title: 'RxJs: Cold vs. hot observables'
created: '2023-10-09T11:58:04.369Z'
modified: '2023-10-09T12:22:33.643Z'
---

# RxJs: Cold vs. hot observables


## Cold vs. hot

**Cold** observables create a new producer for every new consumer (Observable) upon `subscribe(...)` (as part of the observable execution).

**Hot** observables: the producer is created _outside_ the observable execution context.

A cold observable can be made hot.  
A hot observable can NOT be made cold.


## Unicast vs. multicast

**Unicast** = 1 consumer per 1 producer.  
**Multicast** = multiple consumers per 1 producer.

Cold observables are _always_ unicast. 
Hot observables are _mostly_ multicast, but can be unicast as well.


## Making a cold observable hot

**Option 1**: Manually, using a `Subject` as an intermediary:
```
function makeHot(cold: Observable): Observable {
  // Create an orchestrating subject
  const subject = new Subject();

  // Subscribe the created subject to the passed observable
  const rootSubscription = cold.subscribe(subject);

  let refCount = 0;

  return new Observable((observer) => {
    refCount++;

    // When a new observer subscribes to out heated observable,
    // subscribe it to the intermediary subject under the hood
    let childSubscription = subject.subscribe(observer);

    return () => {
      refCount--;
      
      childSubscription.unsubscribe();
      
      if (!refCount) {
        rootSubscription.unsubscribe();
      }
    };
  });
}

const cold = interval(1000);
const hot = makeHot(cold);
```

**Option 2**: Use `share()` operator:
```
const cold = interval(1000);
const hot = cold.pipe(share());
```


