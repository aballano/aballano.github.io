---
layout: post
title: Kotlin Functors, Applicatives, And Monads in Pictures. Part 1/3
categories:
  - Functional Programming
  - Monad
  - Functor
  - Applicative
  - Kotlin
  - Software Development
published: true
---

>> _This is a translation of_ [_Functors, Applicatives, And Monads In Pictures_](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html) _from_ [_Haskell_](https://www.haskell.org/) _into_ [_Kotlin_](https://hackernoon.com/tagged/kotlin)_._

> _Actually this is a translation of_ [_another translation from Haskell to Swift_](http://www.mokacoding.com/blog/functor-applicative-monads-in-pictures/)_._

_I read through the original post and I found it really interesting for learning new concepts of FP, so I decided to do an additional translation (having also some fun in the way)._

_I also wanted to see how far can Kotlin get compared to Swift :)_

_If you enjoy this post be sure to say thanks to the author of the original version:_ [_@\_egonschiele_](https://twitter.com/_egonschiele)

---

Here’s a simple value:

![](https://cdn-images-1.medium.com/max/800/0*cWvHYgZpw85fz_QW.png)

And we know how to apply a function to this value:

![](https://cdn-images-1.medium.com/max/800/0*XphGzXumvbJTBycX.png)

Simple enough. Lets extend this by saying that any value can be in a context. For now you can think of a context as a box that you can put a value in:

![](https://cdn-images-1.medium.com/max/800/0*7ZPc_kKNSlCqiRXR.png)

Now when you apply a function to this value, you’ll get different results **depending on the context**. This is the idea that Functors, Applicatives, Monads, Arrows etc are all based on. The `Option` data type defines two related contexts:

> **_Note:_** _the pictures use Maybe (Just | None) from Haskell, which correspond to a custom Kotlin’s_ Option _(Some | None) implementation._

![](https://cdn-images-1.medium.com/max/800/0*1NCb7mGijOSflxhO.png)
```kotlin
sealed class Option<out A> {  
  object None : Option<Nothing>()  
  data class Some<out A>(val value: A) : Option<A>()`   }`
```
In a second we will see how function application is different when something is a `Some(T)`versus a `None`. First let’s talk about [Functors](https://hackernoon.com/tagged/functors)!

### Functors

When a value is wrapped in a context, you can’t apply a normal function to it:

![](https://cdn-images-1.medium.com/max/800/0*M7c-lRRTz8pKWpN4.png)

This is where `map` comes in (`fmap` in Haskell). `map` is from the street, `map` is hip to contexts. `map` knows how to apply functions to values that are wrapped in a context. For example, suppose you want to apply a function that adds 3 to `Some(2)`. Use `map`:

```kotlin
fun sumThree(n: Int) = n + 3
Option.Some(2).map(::sumThree)   // => Some(5)
```

![](https://cdn-images-1.medium.com/max/800/0*iAU0E7CdHqjfQ9RK.png)

**Bam!** `map` shows us how it’s done! But how does `map` know how to apply the function?

### Just what is a Functor, really?

A Functor is any type that defines how `map` (`fmap` in Haskell) applies to it. Here’s how `map`works:

![](https://cdn-images-1.medium.com/max/800/0*egb47QB6zSr1jbl2.png)

So we can do this:

    Option.Some(2).map { it + 3 }

And `map` magically applies this function, because `Option` is a Functor. It specifies how `map` applies to `Some`s and `None`s:
```kotlin
inline fun <B> map(f: (A) -> B): Option<B> = when (this) {  
    is None -> this  
    is Some -> Some(f(value))  
}
```
Here’s what is happening behind the scenes when we write `Option.Some(2).map { it + 3 }`:

![](https://cdn-images-1.medium.com/max/800/0*2ePtQNke8BGZT5MN.png)

So then you’re like, alright `map`, please apply `{ it + 3 }` to a `None`?

![](https://cdn-images-1.medium.com/max/800/0*w6a1JBnkBrpKlW1N.png)

    Option.None.map { it + 3 }// => None

![](https://cdn-images-1.medium.com/max/800/0*HJUxTnL7aNTGmuG-.png)

Well, there's a gotcha here since the code above doesn't compile. Why? Well because in this case None doesn't have a proper type, so you cannot do a plus with type `Nothing`. But it should be fine because you normally won't write that code but something like:

```kotlin
val option: Option<Int> = someCallThatMightReturnNone()
option.map { it + 3 }// => None
```
Like Morpheus in the Matrix, `map` knows just what to do; you start with `None`, and you end up with `None`! `map` is zen. Now it makes sense why the `Option` type exists. For example, here’s how you work with a database record in a language without `Option`:
```kotlin
val post = Post.findByID(1)
return post?.title
```
But in Kotlin using the `Option` functor:

    findPost(1).map(::getPostTitle)

If `findPost(1)` returns a post, we will get the title with `getPostTitle`. If it returns `None`, we will return `None`!

We can even define `map` as an infix function for (`<$>` in Haskell), and do this instead:

```kotlin
inline infix fun <B> map(f: (A) -> B): Option<B> { ... }
_findPost_(1) map ::getPostTitle
```

> **_Note:_** _we have to use just_ `_map_` _because_ `_<$>_` _wouldn't compile. Another option would be to override a common operator like_ `_/_` _or_ `_*_`

Here’s another example: what happens when you apply a function to an array?

![](https://cdn-images-1.medium.com/max/800/0*lcScORdRNawytDNN.png)

Arrays are functors too\*!

\*Basically Kotlin provides an extension function to all iterables in the form:

```kotlin
inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {..}
```

Okay, okay, one last example: what happens when you apply a function to another function?

```kotlin
{ a: Int -> a + 2 } map { a: Int -> a + 3 }   // => ???
```

Here's a function:

![](https://cdn-images-1.medium.com/max/800/0*fT23UDVq18hzvatb.png)

Here’s a function applied to another function:

![](https://cdn-images-1.medium.com/max/800/0*kKizWpvf5xsrDDaj.png)

The result is just another function!

    typealias IntFunction = (Int) -> Int

So functions can be Functors too! When you use `map` on a function, you’re just doing function composition!

#### Well, that's it for today, I hope you got the idea about what's a Functor. Since the original post was pretty long, I'll continue in the next series with Applicatives. Now go try write some Functors in Kotlin!

#### Wanna play around with the code? Take a look at [https://github.com/aballano/FAM-Playground](https://github.com/aballano/FAM-Playground)

Want more? Go try applicatives in the second part!

[**Kotlin Functors, Applicatives, And Monads in Pictures. Part 2/3**](https://aballano.github.io/kotlin-functors-applicatives-and-monads-in-pictures-part-2-3/ "This is a translation of Functors, Applicatives, And Monads In Pictures from Haskell into Kotlin")

[![](https://cdn-images-1.medium.com/max/400/1*0hqOaABQ7XGPT-OYNgiUBg.png)](http://bit.ly/HackernoonFB)

[![](https://cdn-images-1.medium.com/max/400/1*Vgw1jkA6hgnvwzTsfMlnpg.png)](https://goo.gl/k7XYbx)

[![](https://cdn-images-1.medium.com/max/400/1*gKBpq1ruUi0FVK2UM_I4tQ.png)](https://goo.gl/4ofytp)

> [Hacker Noon](http://bit.ly/Hackernoon) is how hackers start their afternoons. We’re a part of the [@AMI](http://bit.ly/atAMIatAMI) family. We are now [accepting submissions](http://bit.ly/hackernoonsubmission) and happy to [discuss advertising & sponsorship](mailto:partners@amipublications.com) opportunities.

> If you enjoyed this story, we recommend reading our [latest tech stories](http://bit.ly/hackernoonlatestt) and [trending tech stories](https://hackernoon.com/trending). Until next time, don’t take the realities of the world for granted!

![](https://cdn-images-1.medium.com/max/800/1*35tCjoPcvq6LbB3I6Wegqw.jpeg)

By [Alberto Ballano](https://medium.com/@aballano) on [March 28, 2017](https://medium.com/p/c47a1b1ce251).

[Canonical link](https://medium.com/@aballano/kotlin-functors-applicatives-and-monads-in-pictures-part-1-3-c47a1b1ce251)

Exported from [Medium](https://medium.com) on March 3, 2023.