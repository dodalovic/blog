---
title: Extension functions in kotlin
date: 2017-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: true
images:
tags:
  - kotlin
  - software development
---

# About Kotlin

_Kotlin_ language recently gained some traction owing to _Google_ promoting it as a primary language for 
writing android apps. _Kotlin_ has already been for a while in production owing to it’s creator _JetBrains_ 
incorporating it in own tools, such as _Intellij IDEA_ and so forth.

One, amongst many other neat features, is extension functions. In depth documentation can be found on official
 kotlin [website](https://kotlinlang.org/docs/reference/extensions.html).

# About extension functions

In short – extension functions provide us useful tool to extend any existing class, no matter if it’s part of JDK,
 some 3rd party library or even own, custom ones.

Example, taken from this project, is the following one:

{{<highlight kotlin>}}
fun Random.betweenInclusive(lower: Int, upper: Int) = this.nextInt(upper - lower) + lower
{{</highlight>}}

Here we extend `java.util.Random` class from the `JDK`, with `betweenInclusive(lower: Int, upper: Int)` 
extension function. This method helps us generate random integer between two numbers, lower and upper 
bounds, inclusive. Project contains couple of useful extension functions, implemented in 
`rs/dodalovic/extension_functions/Students.kt` kotlin file. Other examples are extending `java.util.List` with 
additional capabilities, taking advantage of functional programming applied on collections.

# Running sample application

Application has [Gradle](https://gradle.org/) build tool packaged inside application, so you’re good to go:

{{<highlight bash>}}
# execute from project root
./gradlew -q -PmainClass=rs.dodalovic.extension_functions.StudentsKt execute
{{</highlight>}}

Output would be similar to:


{{<highlight bash>}}
**** Students average: 3.22 ****

Above average:
[
{'name':'name_4','lastname':'lastname_4','average':'4.0','marks':'[5, 2, 4, 4, 5]'}
,
{'name':'name_5','lastname':'lastname_5','average':'4.4','marks':'[5, 5, 4, 4, 4]'}
,
{'name':'name_7','lastname':'lastname_7','average':'3.4','marks':'[1, 4, 2, 5, 5]'}
]

Below average:
[
{'name':'name_1','lastname':'lastname_1','average':'2.6','marks':'[2, 3, 2, 2, 4]'}
,
{'name':'name_2','lastname':'lastname_2','average':'3.2','marks':'[1, 5, 2, 5, 3]'}
,
{'name':'name_3','lastname':'lastname_3','average':'2.6','marks':'[4, 1, 2, 1, 5]'}
,
{'name':'name_6','lastname':'lastname_6','average':'2.8','marks':'[1, 4, 5, 2, 2]'}
,
{'name':'name_8','lastname':'lastname_8','average':'3.2','marks':'[4, 3, 4, 4, 1]'}
,
{'name':'name_9','lastname':'lastname_9','average':'3.2','marks':'[1, 4, 5, 5, 1]'}
,
{'name':'name_10','lastname':'lastname_10','average':'2.8','marks':'[5, 5, 1, 2, 1]'}
]

Highest mark:
5

Best student:

{'name':'name_5','lastname':'lastname_5','average':'4.4','marks':'[5, 5, 4, 4, 4]'}
{{</highlight>}}

# Calling extensions on null references

It is even possible to call extension on a `null` reference. In extension implementation, we 
can, using `this` keyword, check if reference was `null` or not, and perform any logic based 
upon that. 

Here's an example:

{{<highlight kotlin>}}
fun <T> T?.nullSafeToString(): String {
    return this?.toString() ?: "NULL"
}
 
fun main() {
    println(null.nullSafeToString())
    println("Kotlin".nullSafeToString())
} 
{{</highlight>}}

Source code can be checked out from GitHub [repository](https://github.com/dodalovic/kotlin-extension-functions)

Until next time!