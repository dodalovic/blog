---
title: Factory method pattern in Kotlin
date: 2018-08-15T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - software development
  - software patterns
  - kotlin
---

`Kotlin` language can come quite handy write concise and expressive code, without too much boilerplate, while keeping all advantages of being `JVM` citizen. Let's quickly peek 
into example of creating `Factory` method creational design pattern implementation.

```kotlin
package patterns

interface Gadget {
    fun play()
    fun getInfo()
}

enum class Purpose {
    FUN, WORK
}

class GadgetFactory {
    companion object {
        fun createGadget(purpose: Purpose): Gadget = when (purpose) {
            Purpose.FUN -> object : Gadget {
                private val model = "XBox One S | year: 2016"
                override fun getInfo() {
                    println(model)
                }

                override fun play() {
                    println("I'm having great fun playing $model")
                }
            }
            Purpose.WORK -> object : Gadget {
                private val model = "Macbook Pro 2015 | year: 2015"
                override fun getInfo() = println(model)
                override fun play() {
                    println("I'm working hard using my $model")
                }
            }
        }
    }
}

val purpose: String = args[0]
val gadget = GadgetFactory.createGadget(when (purpose) {
    "FUN" -> Purpose.FUN
    "WORK" -> Purpose.WORK
    else -> throw IllegalArgumentException("No gadget for such purpose :(")
})

println("\n**** GADGET INFO ****\n")
gadget.getInfo()

println("\n**** PLAYING GADGET ****\n")
gadget.play()

```

Example demonstrates simple case where user enters which kind of `Gadget` she/he wants via command line argument, and based on value provided, he gets back some kind of 
`Gadget` dynamically instantiated.

We define `interface` `Gadget` with two methods there. We also define two instances of `enum` type `Purpose`. Based on purpose user defines via command line param - we 
dynamically pick appropriate `Gadget` instance at runtime.

Then we create `GadgetFactory::createGadget` factory method, that will, based on user provided input, pass appropriate instance of `Purpose` `enum` as parameter. 
Compact nature of `Kotlin` code gives us opportunity to express ourselves without unnecessary verbosity. We're using `Kotlin` companion object, due to the fact we don't 
actually need to instantiate `GadgetFactory` and due to the fact that there aren't static methods in `Kotlin` - this kind of emulates that functionality for us.

```shell script
$ kotlinc -script factory-method.kts FUN

**** GADGET INFO ****

XBox One S | year: 2016

**** PLAYING GADGET ****

I'm having great fun playing XBox One S | year: 2016

$ kotlinc -script factory-method.kts WORK

**** GADGET INFO ****

Macbook Pro 2015 | year: 2015

**** PLAYING GADGET ****

I'm working hard using my Macbook Pro 2015 | year: 2015

$ kotlinc -script factory-method.kts UNKNOWN-PURPOSE
java.lang.IllegalArgumentException: No gadget for such purpose :(
 at patterns.Factory_method.<init>(factory-method.kts:37)
```

In case you found it interesting - feel free to subscribe for getting more interesting content.