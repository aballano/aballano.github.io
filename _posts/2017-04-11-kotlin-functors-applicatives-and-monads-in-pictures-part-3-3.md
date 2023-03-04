---
layout: post
title: Kotlin Functors, Applicatives, And Monads in Pictures. Part 3/3
categories:
  - Functional Programming
  - Monad
  - Functor
  - Applicative
  - Kotlin
  - Software Development
published: true
---

First of all, if you didn’t read the previous posts, go do so, otherwise you might be missing some essential concepts!

[**Kotlin Functors, Applicatives, And Monads in Pictures. Part 1/3**](kotlin-functors-applicatives-and-monads-in-pictures-part-1-3/ "This is a translation of Functors, Applicatives, And Monads In Pictures from Haskell into Kotlin")

[**Kotlin Functors, Applicatives, And Monads in Pictures. Part 2/3**](kotlin-functors-applicatives-and-monads-in-pictures-part-2-3/ "This is a translation of Functors, Applicatives, And Monads In Pictures from Haskell into Kotlin")

### Monads

How to learn about Monads:

1.  Get a PhD in computer science.
2.  Throw it away because you don’t need it for this section!

Monads add a new twist.

[Functors](https://hackernoon.com/tagged/functors) apply a function to a wrapped value:

![](https://cdn-images-1.medium.com/max/800/0*Ify-0OWj6YL7Biyp.png)

Applicatives apply a wrapped function to a wrapped value:

![](https://cdn-images-1.medium.com/max/800/0*FNa0IqM3n016ufYh.png)

Monads apply a function that returns a wrapped value to a wrapped value. Monads have a function `flatMap` (`liftM` or `>>=` in Haskell) to do this.

```kotlin
inline fun <A, B> Option<A>.flatMap(f: (A) -> Option<B>): Option<B> =   
    when (this) {  
        is None -> this  
        is Some -> f(value)  
    }
```
Let’s see an example. Good old Option (`Maybe` in Haskell) is a monad:

![](https://cdn-images-1.medium.com/max/800/0*kdpByXzPXXNgJ4v8.png)

Just a monad hanging out.

Suppose `half` is a function that only works on even numbers:
```kotlin
fun half(a: Int) = when {  
    a % 2 == 0 -> Some(a / 2)  
    else -> None  
}
```
![](https://cdn-images-1.medium.com/max/800/0*bM7sbALwfuSmwoj7.png)

What if we feed it a wrapped value?

![](https://cdn-images-1.medium.com/max/800/0*I66ezQb2qu243RLh.png)

We need to use `flatMap` (`>>=` in Haskell) to shove our wrapped value into the function. 

Here’s a photo of `flatMap`:

![](https://cdn-images-1.medium.com/max/800/0*MSDfsNNSfXqkXVYa.jpg)

Here’s how it works:
```kotlin
Some(3).flatMap(::half)  
// None  
Some(4).flatMap(::half)  
// Some(2)  
None.flatMap(::half)  
// None
```
What’s happening inside? Let’s look at `flatMap`'s (`>>=` in Haskell) signature again:

```kotlin
inline fun <A, B> Option<A>.flatMap(f: (A) -> Option<B>): Option<B>
```

![](https://cdn-images-1.medium.com/max/800/0*G8_Zuhcn94-9OR0Z.png)

So `Option` is a Monad. Here it is in action with a `Some(3)`!

![](https://cdn-images-1.medium.com/max/800/0*yWziebMi8kBhH4Sn.png)

And if you pass in a `None` it’s even simpler:

![](https://cdn-images-1.medium.com/max/800/0*bJFoxQ-Ylp2SIQg2.png)

You can also chain these calls (by using an infix version of `flatMap`):

```kotlin
Some(20) flatMap ::half flatMap ::half flatMap ::half  
// => None
```

![](https://cdn-images-1.medium.com/max/800/0*N-dZjxCsHwnK_KlT.png)

> _Note: the original article now describes Haskell’s_ `_IO_` _Monad._ [_Kotlin_](https://hackernoon.com/tagged/kotlin) _doesn't have anything like that so this translation skips it._

### Conclusion

1.  A functor is a type that implements `map`.
2.  An applicative is a type that implements `apply`.
3.  A monad is a type that implements `flatMap`.
4.  `Option` implements `map` and `flatMap`, plus we can extend it to implement `apply`, so it is a functor, an applicative, and a monad.

What is the difference between the three?

![](https://cdn-images-1.medium.com/max/800/0*eElNlhORdSeUxp6D.png)

*   **functors**: you apply a function to a wrapped value using `map`.
*   **applicatives**: you apply a wrapped function to a wrapped value using `apply`.
*   **monads**: you apply a function that returns a wrapped value, to a wrapped value using `flatMap`.

So, dear friend (I think we are friends by this point), I think we both agree that monads are easy and a SMART IDEA(tm). Now that you’ve wet your whistle on this guide, why not pull a Mel Gibson and grab the whole bottle. Check out LYAH’s [section on Monads](http://learnyouahaskell.com/a-fistful-of-monads). There’s a lot of things I’ve glossed over because Miran does a great job going in-depth with this stuff.

> _Thanks for reading through this article series, if you have any feedback, suggestion, or error to report please tweet me_ [_@aballano_](http://twitter.com/aballano)_, or leave a comment below._

> _If you want to play around with the code head over to GitHub and_ [_clone the Playground_](https://github.com/aballano/FAM-Playground)

> _Once again, thanks_ [_Adit_](https://twitter.com/_egonschiele) _for the wonderful post, and for all the other great ones on_ [_the blog_](http://adit.io/index.html)_._

Happy coding!

[![](https://cdn-images-1.medium.com/max/400/1*0hqOaABQ7XGPT-OYNgiUBg.png)](http://bit.ly/HackernoonFB)

[![](https://cdn-images-1.medium.com/max/400/1*Vgw1jkA6hgnvwzTsfMlnpg.png)](https://goo.gl/k7XYbx)

[![](https://cdn-images-1.medium.com/max/400/1*gKBpq1ruUi0FVK2UM_I4tQ.png)](https://goo.gl/4ofytp)

> [Hacker Noon](http://bit.ly/Hackernoon) is how hackers start their afternoons. We’re a part of the [@AMI](http://bit.ly/atAMIatAMI) family. We are now [accepting submissions](http://bit.ly/hackernoonsubmission) and happy to [discuss advertising & sponsorship](mailto:partners@amipublications.com) opportunities.

> If you enjoyed this story, we recommend reading our [latest tech stories](http://bit.ly/hackernoonlatestt) and [trending tech stories](https://hackernoon.com/trending). Until next time, don’t take the realities of the world for granted!

![](https://cdn-images-1.medium.com/max/800/1*35tCjoPcvq6LbB3I6Wegqw.jpeg)

By [Alberto Ballano](https://aballano.github.io/) on [April 11, 2017](https://medium.com/p/832d58d92445).

[Canonical link](https://medium.com/@aballano/kotlin-functors-applicatives-and-monads-in-pictures-part-3-3-832d58d92445)

Exported from [Medium](https://medium.com) on March 3, 2023.