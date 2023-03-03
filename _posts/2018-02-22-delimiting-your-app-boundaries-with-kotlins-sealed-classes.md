---
layout: post
title: Delimiting your app boundaries with Kotlin's sealed classes
categories:
  - Kotlin
  - Software Development
published: true
---

![](https://raw.githubusercontent.com/aballano/aballano.github.io/master/images/delimiting-your-app-boundaries-with-kotlins-sealed-classes.webp)

---

Some of us might run into a common problem when having to present data to the user. That is, encoding real life's boundaries into a language that might not reflect reality as we see it.

Imagine that we have a view with a presenter and we want to initialize this one with an User's info and display some data, we would have the following model:

```kotlin
data class User(  
    val id: String,   
    val name: String,   
    val surname: String  
)
```
And the presenter:

```kotlin
fun showUserInfo(user: User) {  
    _displayUserName_(user.name)  
    _displayUserSurname_(user.surname)  
    //(...)  
}
```

This code is fine; we have our preloaded user, so we just show their information to the user. But what happens when we navigate from a deep link or somewhere else where we don't have the full user but its ID? What if we need to fetch it first? We would have this case:

```kotlin
fun showUserInfo(id: String) {  
    val user = _fetchUser_(id)  
    // we ignore the threading part   
    // as it's irrelevant for the purpose of the article  
    _displayUserName_(user.name)  
    _displayUserSurname_(user.surname)  
    //(...)  
}
```
To make this distinction, we need an initialization point from where to call from the view, to reduce the amount of logic it contains. Let's also do a small refactor to remove this last function to reduce the amount of duplicated code. The result is as follows:

```kotlin
fun init(id: String?, user: User?) {  
    val validUser: User = when {  
        id != null -> _fetchUser_(id)  
        user != null -> user  
    }  
  
    _showUserInfo_(validUser)  
}  
  
private fun showUserInfo(user: User) {  
    _displayUserName_(user.name)  
    _displayUserSurname_(user.surname)  
    //(...)  
}
```

As you can see, there are several problems with this approach:

-   This is not pretty, as the view implementation or whoever calls init needs to pass a parameter while making the other explicitly null (we could benefit from default parameters here, but it will lead to the same next problem)
-   There's one case missing! That code won't compile because what happens if you pass two nulls here? Is that a real case scenario anyway? We need to change the  `when`  statement to this:

```kotlin
when {  
    id != null -> _fetchUser_(id)  
    user != null -> user  
    else -> crashWithInvalidDataException()  
}
```

Ok, so 5 lines in our new shiny class and we're already crashing for a case that "should never happen", not too good :(

## But this won't happen in a real case scenario!

Yes, we "all" know, right? Still, this implementation (and probably the tests too)  **tells a different story**. What do you think will happen when a new person in your team reads this code? Are you gonna start explaining all occurrences of this kind of solution as part of the onboarding plan?

There's an apparent  **mismatch about what the real specification is and what the code is doing**, and that leads to potential errors that "should never happen", although they sometimes do, and to useless boilerplate code or exceptions thrown meant to cover this lack of boundaries of your system.

## Defining your program boundaries

Some of you might have be thinking of the perfect Kotlin tool to solve this issue, and for the ones that didn't yet, let's try to put that thought in your brain :)

Whenever I find myself in this kind of situations where real life have some boundaries that cannot be expressed easily in code. A magical, beautiful words come to my mind:  `sealed class`

Yes! Sealed classes, because they have all we need for these scenarios: limited boundaries (such as enums) and a variety of parameters.

Let's start defining our boundaries first, we can have either an already fetched User or an id, so here we go:

```kotlin
sealed class UserData {  
    data class Model(val user: User) : UserData()  
    data class Id(val id: String) : UserData()  
}
```

Easy, right? Now let's modify our init function

```kotlin
fun init(userData: UserData) {  
    val validUser: User = when(userData) {  
        is UserData.Model -> userData.user  
        is UserData.Id -> _fetchUser_(userData.id)  
    }  
  
    _showUserInfo_(validUser)  
}
```
And we're done! Not only we managed to remove the else case, but we also made our code more bulletproof and readable.

Hope you enjoyed the article, feel free to send suggestions or comments via social networks below!.

Happy coding!

P.S. Thanks [pablisco](https://medium.com/u/b276785d55d2?source=post_page-----cd7c40974078--------------------------------) for proofreading the article.