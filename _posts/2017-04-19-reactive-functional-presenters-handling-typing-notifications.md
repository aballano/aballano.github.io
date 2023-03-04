---
layout: post
title: 'Reactive Functional Presenters: Handling Typing notifications.'
categories:
  - RxJava
  - Android
  - Software Development
published: false
---


Working on a messenger app is great, it's both complicated and fun. It sometimes requires you to try to figure it out how some giants in the market, like Whatsapp, Telegram or Facebook, dealt with some scenarios and challenges. At the same time you to still try to build a solution that fits your specific product.

In my last post I explained a  [reactive way of dealing with errors](https://tech.xing.com/reactive-views-retrying-errors-a59fffbd827f)  and today I'd like to share a specific, yet complex, feature: Incoming typing notifications.

The logic behind incoming typing notifications might sound pretty easy:  **While another user is typing, show it in the chat view**. But turns out it's a bit more complicated. How do you know when the other user stopped typing? You might wait for the backend to tell you that. But that’s a waste of resources and also what happens if you lost the connection? What you need is a timeout. Take a look at this diagram:

![](https://miro.medium.com/v2/resize:fit:492/1*3t8ty9mdlnYT3F2x18kdCw.png)

Simplified approach on how typing notifications works

Now let me break this down a bit: we will have 1 starting event and 2 stopping events.

-   The starting event (blue) is another user typing. At that point the first thing we have to do is add this user to a list of typing users and then update our view to show that, why? Because many users could be typing at the same time, so we want to show different view states:  _"John is typing…"_,  _"2 users are typing…"_, etc.
-   One possible stopping event (orange) is whenever the typing user sends a message. At that point we know that he/she's no longer typing, so we can safely remove from the list and update the view again. In case that user start typing again we will receive a typing event (blue) and start from the beginning.
-   The other possible stopping event (purple) is the mentioned timeout: a safeguard in case that we loose the connection. Basically we're gonna have a timeout of about 5 seconds and, after that, if we didn't received any new typing events from that user, we will assume that he/she stopped typing.

I'll admit I was heavily influenced by  [Pacoworks](https://twitter.com/pacoworks)  and  [his talk about memory management in fully reactive apps](http://www.pacoworks.com/2017/03/11/about-memory-management-in-fully-reactive-apps-at-droidcon-bytes-february-17/), so for this approach I wanted to play a bit with functional programming and specially with pure(ish) functions. That way I could remove a global state from the presenter, so everything is encapsulated inside the Rx chain. Because of that there is a single entry point to the Presenter, which is omitted here. That entry point calls this method:

> Note: For simplicity I removed the entry point from the code. There I basically create the State and return a Disposable to the view, so the signature looks like:
> 
> `public static Disposable initialize(Configuration config)`

There are several things to mention here:

-   The function is static, that means that it only deals with the given inputs and doesn't modify any global state.
-   We're given a Configuration and a State. The Configuration is a wrapper around our dependencies, something similar to a  [Reader monad](https://medium.com/@JorgeCastilloPr/kotlin-dependency-injection-with-the-reader-monad-7d52f94a482e)  but worse. The State is nothing more than that: it contains our current typing users list.
-   Although our State and Configuration are immutable, there's one single thing that is not! Did you see it? Exactly, is the  `currentlyTypingUser`  because we need to hold the count of typing users and, therefore, we need to add and remove elements from it.
-   Although the function returns a  `Completable`  it will keep listening for incoming notifications even if the  `Completable`  in  `flatMapCompletable`  completes, why? Because  `flatMapCompletable`  completes when both the upstream and the given  `Completable`  terminates, but our  `Observable`  never does that by default.

OK, this basically covers the entry point I showed before, let's continue with the scheduling:

This basically covers our stop event by doing an  `amb`  (takes the first terminate event) of our 2 stopping events, so we remove the specific typing user and update the view again. Let's take a look at the timeout now:

What we have here is basically an initial event plus a subscription to the next typing events of the same user (note that I simplified that part in the filter). Now we  `switchMap`  to a  **timer.**  This means that we start the timer with the initial event and  **it will be reset**  every time we receive an additional typing event (note that this will be done here and not in our initial setup method). After that we take the first value and return a  `Completable`.

Since our  `notificationManager`  provides a never-ending Observable but we're only interested on getting the first timeout, we just  `take(1)`  to make it complete and ignore the emission.

Let's see the final piece of the puzzle:

Really simple, right? Listen for new messages of this specific user and do the same completable-trick™.

## Summary

With this we covered mostly all of our initial diagram, right? But what happens with that green and red actions in it? The trick with them is that they're modifying a state and then updating the view, so there are chances of having a race condition!!

![](https://miro.medium.com/v2/resize:fit:900/1*FXV0FJf1IgH64FqU0jF6fA.gif)

Any dev dealing with race conditions

Fortunately for us this 2 sections of our code will be executed in the main thread, so the entire block should be thread safe. But is good to have them identified in case that changes at some point.

## Bonus point: Tests

A bonus point of this approach is in the tests: basically this class is tested through a single-static entry point, meaning that we give inputs and we test "outputs", in this case being our outputs interactions with the view of this form:

`verify(view).updateOneParticipantTyping()`

And there you go, there's no need of testing any state, which allows us to change mostly everything inside the class without touching a single test.

That was it, I hope you liked the post and if you did please hit the recommend button below and subscribe for future posts!