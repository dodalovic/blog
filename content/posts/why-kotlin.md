---
title: Why Kotlin?
date: 2019-10-04T14:54:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
 - programming languages
 - featured
 - kotlin
---

{{< figure src="/posts/img/why-kotlin.png" position="center" style="border-radius: 8px;" >}}

In this, relatively short post, I’ll try to cover some aspects of programming in [Kotlin programming language](https://kotlinlang.org/) that may give you better context when deciding upon whether you may like to use it for your next project. 

Let’s get started.

## Positive sides
Kotlin, owing mostly to the Android community, has now gone a long way from it’s inception. 
This language is not a new thing. As many other languages - it’s been used by 
[many big companies](https://medium.com/@daveford/who-is-using-kotlin-84b11b4fb51a) nowadays. It is a very 
active Open Source project, on top. 

From my own experience, listed just below could be the most compelling reasons to go for Kotlin.

### Statically typed language

Kotlin is often compared to Java, and one commonality between the two is that they are both statically typed languages, where type of each variable is known at the compile time. 
The advantage of statically typed languages comes into picture on medium to large size projects where having dynamic language instead would require great discipline and even greater - testing. People are not known for their discipline, so that’s why I personally go for statically typed languages.

### Nullability in the type system

{{< figure src="/posts/img/nullability.png" position="center" style="border-radius: 8px;" >}}

Kotlin, compared to Java, for instance, has nullability expressed in it’s [type system](https://kotlinlang.org/docs/reference/null-safety.html). That means that, when using Kotlin, you constantly need to think about nullability of your variables. This may be, at the beginning, a bit frustrating, coming from Java, which has no such a concept. 
When writing applications, completely written in Kotlin, you will eliminate runtime exceptions, which is great. Most of the time, however, you need to interact with Java libraries, which makes the things a bit more complicated, since Kotlin compiler needs some help from your side, to decide on nullability of some variables (see [platform types](https://kotlinlang.org/docs/reference/java-interop.html)). The good thing about the Kotlin compiler is that it respects nullability annotations found in byte-code of the Java code we are using, so that helps it to decide on nullability of variables when interacting with Java.

### Adoption in existing frameworks / tooling

By analyzing current frameworks in the JVM ecosystem, we can spot that Kotlin is an officially supported language in all the major players in this area: **Spring**, **Vert.x**,  **Micronaut**, **Quarkus**, etc.

Gradle build tool has promoted Kotlin as a tool to describe build scripts, instead of Groovy. The community out there is, from my personal experience, quite big, so you won’t be left alone in case you come across some difficulty when using Kotlin in your favorite framework. 

### IDE support

As expected, Intellij IDEA could be considered the right tool when developing applications using Kotlin. The good news is that Kotlin support is shipped with the Community edition of Intellij, so purchasing license is not required. 

Developer experience using Kotlin in IDEA is quite impressive. From my own experience, IDEA gives you a lots of hints how to improve your existing code, which helps you learn the most idiomatic way to write Kotlin code. If I may say - the way IDEA assists when coding Kotlin is even better than Java support , if that is possible, at all 😊 !

No need to panic - Kotlin support is provided for Eclipse, Visual Studio Code and others, as well.

### Kotlin coroutines

[Kotiln coroutines](https://kotlinlang.org/docs/reference/coroutines-overview.html) are the way to tackle concurrency from another perspective. Coroutines are not a part of standard library, but rather additional library you need to include in case you need it for your projects. 

Coroutines are the concept that is not new at all, and has been used by many other languages. In it’s shortest form, coroutines could be explained as a tool to write highly concurrent software, using relatively small number of native threads, using familiar programming model that resembles sequential processing. 

Concurrent programming using APIs from the standard Java SDK is not something most of engineers enjoy using. Another alternative would be to use [reactive extensions](http://reactivex.io/) which allow for rich set of transformations on asynchronous data, combining multiple asynchronous executions, error handling, etc. 

The issue with reactive extensions is that their APIs are not intuitive, they are far away from the most intuitive programming model of synchronous execution. 

Having said all of this - Kotlin coroutines are a very powerful tool to use, but they require time and practice in order to master them. 

### Functions as a first grade citizens

As opposed to Java - functions in Kotlin are really first class citizens: 

* variables can be of a function type
* functions can receive other functions as parameters
* classes can implement functions, etc. 

This feature provide us a really powerful tool to design complex systems with a minimum boilerplate. 
We can implement software patterns with zero overhead. 

### Standard library replaces Apache, Guava, Lombok

Kotlin standard library is feature rich library which ships with the features which Java developers used to find by integrating various third party libraries in their projects. So, in most of Java projects one could find libraries such as: various Apache libraries, Guava, Lombok, etc. Kotlin standard library pretty much eliminates the need for such a libraries. Standard library, with less than 1MB size additionally shrinks down size of your deliverables, making it even more container friendly. 

## Negative sides

{{< figure src="/posts/img/negative.png" position="center" style="border-radius: 8px;" >}}

As with any other technology, Kotlin must also have some disadvantages. 
Talking from personal experience, I was able to come up with only one single drawback when using it.

### Slower compilation time compared to Java

One downside I personally experienced was the fact that compilation time was higher compared to pure Java based projects. This was especially the case when I developed mixed Java and Kotlin code base. This increased compilation time was something that developer gets affected with, but that’s something that is not so significant that it would be a deal breaker. 
In pure Kotlin projects, compilation was decently faster, but still slower than Java projects compilation.

It is expected that Kotlin compiler needs to work hard to translate all the feature rich code into Java bytecode, and Kotlin team is working hard on improving compile time. 

Compile time , on the other side, is already dramatically improved when compared to previous Kotlin releases. Recently they published information that they are organizing hackatrons where people will try to find creative ways to significantly improve current compile times. 

## Conclusion

Speaking from own experience - good developers build quality software, bad ones develop bad software. In no matter which language used. 

But what is nice about Kotlin as such is that it had enough time to address some issues that software development industry was facing so far and they decided to design the language around some best practices which emerged, like immutability, nullability in the type system, etc. 

Due to seamless interoperability with existing Java ecosystem and having an opportunity to  use this enormous power it brings, Kotlin these days shows potential to become a mainstream JVM programming language some time in the future. 

Until next time👋 
