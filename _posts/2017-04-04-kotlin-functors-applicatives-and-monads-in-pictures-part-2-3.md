---
layout: post
title: Kotlin Functors, Applicatives, And Monads in Pictures. Part 2/3
categories:
  - Functional Programming
  - Monad
  - Functor
  - Applicative
  - Kotlin
  - Software Development
published: false
---

First of all, if you didn't read the previous post, go do so, otherwise you might be missing some essential concepts!

[**Kotlin Functors, Applicatives, And Monads in Pictures. Part 1/3**](https://aballano.github.io/kotlin-functors-applicatives-and-monads-in-pictures-part-1-3/ "This is a translation of Functors, Applicatives, And Monads In Pictures from Haskell into Kotlin")

### Applicatives

Applicatives take it to the next level. With an applicative, our values are wrapped in a context, just like [Functors](https://hackernoon.com/tagged/functors):

![](https://cdn-images-1.medium.com/max/800/0*tLlU2WEqCu7jYUSj.png)

But our functions are wrapped in a context too!

![](https://cdn-images-1.medium.com/max/800/0*-3EM_WBCQu5KlCUX.png)

Yeah. Let that sink in. Applicatives don’t kid around. Unlike Haskell, [Kotlin](https://hackernoon.com/tagged/kotlin) doesn’t have _yet_ a built-in way to deal with Applicatives. But it is very easy to add one! We can define an `apply` function for every type supporting Applicative, which knows how to apply a function wrapped in the context of the type to a value wrapped in the same context:
```kotlin
infix fun <A, B> Option<(A) -> B>.apply(f: Option<A>): Option<B> =  
        when (this) {  
            is Option.None -> Option.None  
            is Option.Some -> f.map(this.value)  
        }

infix inline fun <A, reified B> Array<(A) -> B>.apply(a: Array<A>) =  
        Array(this.size \* a.size) {  
            this\[it / a.size\](a\[it % a.size\])  
        }
```
If both `this` and the function are `Some`, then the function is applied to the unwrapped option, otherwise `None` is returned.

For the `Array` I'm using its constructor parameters to generate the array, although note that this wouldn't be the most performant choice for bigger arrays.

> **Note:** this would be the`<*>` in Haskell, so again we could use a `*` operator if needed.

![](https://cdn-images-1.medium.com/max/800/0*YZDbwqs5Vxy-ldbA.png)

```kotlin
Some({ a: Int -> a + 3 }) apply Some(2)`   // => Some(5)`
```

---

If you look carefully you will see that our operator only works in this specific order: `Option(function) apply Option(value)` why? Because our extension function is defined in that order. Then, couldn’t I just make another extension function to work the other way around, like:

```kotlin
fun <A, B> Option<A>.`apply`(o: Option<(A) -> B>) = {...}  
fun <A, B> Option<(A) -> B>.`apply`(o: Option<A>) = {...}
```

Technically not. Since Kotlin produces same code as Java would, it has to deal with our friend the [type erasure](https://docs.oracle.com/javase/tutorial/java/generics/erasure.html). So basically those 2 functions would loose the generic types at compile time (and become just `Option`), making them equal and therefore invalid. But here’s a trick, we could use a so called `dummyImplicit` (and you can read why [here](http://stackoverflow.com/questions/34745066/dummyimplicits-is-this-used-and-how/34746255?stw=2#34746255)) so it would look like:

```kotlin
fun <A, B> Option<A>.apply(f: Option<(A) -> B>, dummyImplicit: Any? = null): Option<B> =   
    when (this) {  
        is Option.None -> Option.None  
        is Option.Some -> f.map { it(value) }}
```

This allows us to do:

```kotlin
Some(2).apply(Some({ a: Int -> a + 3 }))  // => Some(5)
```
But unfortunately it makes the infix impossible since infix functions only have one parameter :(

---

So, following our previous explanation, using _apply_ can lead to some interesting situations. For example:
```kotlin
arrayOf<(Int) -> Int>({ it + 3 }, { it * 2 }) _apply_ arrayOf(1, 2, 3)  // => [ 4, 5, 6, 2, 4, 6 ]
```

![](https://cdn-images-1.medium.com/max/800/0*dTv-_gpKc-DbXIx7.png)

> **_Note:_** _the original article now shows how Applicatives are more powerful than Functors in that they allow function application with multiple parameters. Again this is not feasible in vanilla Kotlin, but we can work around it by defining the function we want to handle in a_ [_curried way_](https://en.wikipedia.org/wiki/Currying)_._

Here’s something you can do with Applicatives that you can’t do with Functors. How do you apply a function that takes two arguments to two wrapped values?
```kotlin
fun curriedAddition(a: Int) = { b: Int ->  
    a + b  
}

Some(3) map ::curriedAddition map Some(2)`   // => COMPILER ERROR`
```
Applicatives:
```kotlin
Some(3) map ::curriedAddition apply Some(2)  
// => Some(5)
```
Wait, what?? Let me break it down step by step:
```kotlin
Some(3) map ::curriedAddition  
// => Some({ 3 + b })
```
```kotlin
Some({ 3 + b }) apply Some(2)  
// => Some(5)
```
`Applicative` pushes `Functor` aside. “Big boys can use functions with any number of arguments,” it says. “Armed with map and apply, I can take any function that expects any number of unwrapped values. Then I pass it all wrapped values, and I get a wrapped value out! AHAHAHAHAH!”

```kotlin
fun curriedTimes(a: Int) = { b: Int -> a * b }

Some(3) map ::curriedTimes apply Some(5)  
// => Some(15)
```

But wait, what if we want to go deeper?

![](https://cdn-images-1.medium.com/max/800/1*cmUv1wycDkmX3CQWYBRm2g.png)

Suppose we have a triple product function:
```kotlin
fun tripleProduct(a: Int, b: Int, c: Int) = a * b * c
```
We want to do the same as before but we don't want to manually curry it, so we have a curry function, in this case for 3 params:

```kotlin
fun <A, B, C, D> curry(f: (A, B, C) -> D): (A) -> (B) -> (C) -> D = { a -> { b -> { c -> f(a, b, c) } } }
```
> Thanks [Ľuboš Mudrák](https://medium.com/u/e856388adc0e) for the suggestion!

Well, that's easy:
```kotlin
Some(3) map _curry_(::tripleProduct) apply Some(5) apply Some(4)  
// => Some(60)
```

#### That’s it! Hope you enjoyed this second part as much as I did while writting it, remember that you can find all the code in the [playground repo I created](https://github.com/aballano/FAM-Playground) and hope to see you in the next and final series!

The third and final part is already available:

[**Kotlin Functors, Applicatives, And Monads in Pictures. Part 3/3**](https://aballano.github.io/kotlin-functors-applicatives-and-monads-in-pictures-part-3-3/ "This is a translation of Functors, Applicatives, And Monads In Pictures from Haskell into Kotlin")

[![](https://cdn-images-1.medium.com/max/400/1*0hqOaABQ7XGPT-OYNgiUBg.png)](http://bit.ly/HackernoonFB)

[![](https://cdn-images-1.medium.com/max/400/1*Vgw1jkA6hgnvwzTsfMlnpg.png)](https://goo.gl/k7XYbx)

[![](https://cdn-images-1.medium.com/max/400/1*gKBpq1ruUi0FVK2UM_I4tQ.png)](https://goo.gl/4ofytp)

> [Hacker Noon](http://bit.ly/Hackernoon) is how hackers start their afternoons. We’re a part of the [@AMI](http://bit.ly/atAMIatAMI) family. We are now [accepting submissions](http://bit.ly/hackernoonsubmission) and happy to [discuss advertising & sponsorship](mailto:partners@amipublications.com) opportunities.

> If you enjoyed this story, we recommend reading our [latest tech stories](http://bit.ly/hackernoonlatestt) and [trending tech stories](https://hackernoon.com/trending). Until next time, don’t take the realities of the world for granted!

![](https://cdn-images-1.medium.com/max/800/1*35tCjoPcvq6LbB3I6Wegqw.jpeg)

By [Alberto Ballano](https://medium.com/@aballano) on [April 4, 2017](https://medium.com/p/f99a09efd1ec).

[Canonical link](https://medium.com/@aballano/kotlin-functors-applicatives-and-monads-in-pictures-part-2-3-f99a09efd1ec)

Exported from [Medium](https://medium.com) on March 3, 2023.