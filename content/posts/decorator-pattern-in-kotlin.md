---
title: Decorator pattern in kotlin
date: 2017-07-23T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - kotlin
  - software development
  - software patterns
---

If you feel curious how would an implementation of decorator design pattern look like in [Kotlin](https://kotlinlang.org/), 
this might be the right place for you. This example is just a very basic thing that you then tweak until 
it’s perfect. Pattern definition can be found at [Wiki](https://en.wikipedia.org/wiki/Decorator_pattern), but what’s
 important is that you can compose chain of decorators at runtime and in such a way – you can control runtime 
 behavior of your system.

{{<highlight kotlin>}}
package patterns

interface CarService {
    fun doService()
}

interface CarServiceDecorator : CarService

class BasicCarService : CarService {
    override fun doService() = println("Doing basic checkup ... DONE")
}

class CarWash(private val carService: CarService) : CarServiceDecorator {
    override fun doService() {
        carService.doService()
        println("Washing car ... DONE")
    }
}

class InsideCarCleanup(private val carService: CarService) : CarServiceDecorator {
    override fun doService() {
        carService.doService()
        println("Cleaning car inside ... DONE")
    }
}

fun main(args: Array<String>) {
    val carService = InsideCarCleanup(CarWash(BasicCarService()))
    carService.doService()
}
{{</highlight>}}

Program output:

{{<highlight shell>}}
Doing basic checkup ... DONE
Washing car ... DONE
Cleaning car inside ... DONE
{{</highlight>}}

In case you liked this post – feel free to subscribe to get more interesting content coming soon.