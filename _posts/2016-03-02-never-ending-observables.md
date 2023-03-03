---
layout: post
title: Dealing with RxJava's never-ending Observables
categories:
  - Rxjava
  - Testing
  - Android
  - Software Development
published: true
---
---
layout: post
title: Dealing with RxJava's never-ending Observables
categories: [Rxjava, Testing, Android, Software Development]
---

## Dealing with RxJava's never-ending Observables

Never-ending Observables are those that observe some data indefinitely until you unsubscribe from them. They're quite useful in many situations, specially when dealing with databases as you can observe emissions from a specific table, for example. A good example of this is [SqlBrite](https://github.com/square/sqlbrite).

When using this kind of observables we need to take care on which operators to use on them since you might run into problems. This is specially dangerous if you, for example, want to migrate DB query using Single to an Observable, because the operators don't always work the same way…


### The scenario

Let's imagine this simple[^1] case:

<script src="https://gist.github.com/aballano/f43ad118acc0b6683a2563753b64f558.js"></script>


Now a typical test could be as following:

<script src="https://gist.github.com/aballano/51db624e734bf3d2b781f70bdc81069b.js"></script>


**Now is QUIZZ time! What will this test output?**

1. Success
2. Failure


---


### The problem

If you answered the first, you're right: this test will pass even though the production code is wrong!

> "Wait, what?? But my test just passed!"

Indeed it does! But why? This is a bad combination of using the wrong **operator** and **test** implementation.
Let's first take a look at our test and figure it out why *it's passing when it should be failing*.


### The wrong testing approach

Ok, so at this point we might have discovered that our app doesn't really work when running this code, but our tests say the other way, so where's the catch?

As stated in the `getUserList()`  function comment:

`//some DB query observing changes`

This means that, for this specific case of Observing DB changes, you need an Observable that will emit, via onNext the initial value and all subsequent values **every time** the DB is updated. This means that this observable will **never complete**.

Then, if you take a closer look at our test you could spot the problem:

```kotlin
whenever(localDataSource.observeUserList())
    .thenReturn(Observable.just(USER_LIST))
```

Again, just checking the docs this time, you could see that Observable.just emits a single value and then completes! This mean that our test scenario is not the same as our real scenario, since we would need an observable that never completes. Luckily this has an easy fix by creating a utility function:

<script src="https://gist.github.com/aballano/2f70eb24940934430e0fedc2591fb91f.js"></script>

Alternatively you could also use an Rx `Subject` (thanks [@pacoworks](https://twitter.com/pacoworks) for the hint).

Ok, so our test now matches our real scenario and now we can see it **failing**! Now the question is, why?

### The wrong operator

The answer will look simple for the ones that already noticed or had problems with this before:

An observable that never completes cannot be used with **certain operators**, as some of them require the observable to actually **complete** in order to do their job. An example of that is the `toList()` operator that we're using. Taking a look inside we can see this piece of code:

```kotlin
@Override
public void onNext(T t) {
    collection.add(t);
}

(...)

@Override
public void onComplete() {
    U c = collection;
    collection = null;
    actual.onSuccess(c);
}
```

So this makes quite clear that the `toList()` operator only works on Observables that **have an end** (which, by the way, seems pretty obvious once you think about it).

Fixing this specific case shouldn't be too difficult as you could map the list directly if you're using Kotlin or play around with conversions to Single using the `firstOrError()` operator on the given observable, example:

```kotlin
getUserList()
    // Get the first emission (List<User>)
    .firstOrError()
    // Now fallback to Observable as Single doesn't                            
    // have flatMapIterable
    .toObservable() 
    // Transform into Observable<User> with multiple emissions
    .flatMapIterable { it }
    .map { user -> user.name }
    .toList()
```

Or even simpler if you use Kotlin's map:

```kotlin   
getUserList()
    // List<User> -> List<String>
    .map { it.map { it.name } }
    // Get the first emission
    .firstOrError()
```

---

### Recap

Messing with Rx if we don't really know what an operator does or what kind of Observable we have (finite or never-ending) is relatively easy and that combined to a wrong test implementation could lead to errors in production. For that I have two tips:

1. Try to identify the type of your Observable:
  If it's a finite one (one that completes) use `Observable.just` for your tests.
  If it's an infinite/never-ending one (one that doesn't complete) use `Observable.create` as shown.

2. In order to avoid this issues with RxJava is always good to **take a look to the docs**, even if you think you know what you're doing.

Hope you liked or learned something new with this article, if so please hit the clap button below.
Thanks for reading!


> This post was migrated from [Medium](https://tech.new-work.se/dealing-with-rxjavas-never-ending-observables-1ca8f18797ff)


---


[^1]: This can be solved in other ways, but this example is shown here for simplicity purposes.
