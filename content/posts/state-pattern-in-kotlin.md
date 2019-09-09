---
title: Zero boilerplate implementing state pattern in kotlin
date: 2017-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - kotlin
  - software development
  - software patterns
---

[*State pattern*](https://en.wikipedia.org/wiki/State_pattern) is a pretty nice solution for solving
 problems related to changing behavior of some component(class) at runtime, which varies depending on
  current state of that object. Clients of our class having dynamic behavior have an impression that, 
  upon interaction with that class, it seems like there’s different implementation of that object as of 
  that interaction. There’s no magic involved – we’re just using composition, and our dynamic object just 
  delegates call to `State` object it encapsulates. Interface of `State` object defines set of actions 
  (methods) that change behavior of our wrapping class (set of actions that cause system to transition). 
  `State` implementations are those that are in charge of making transitions from state to state.

Here’s an example of coffee machine, that acts differently based on state it’s currently in. 
Here, `CoffeeMachine` is our dynamic class, and it encapsulates instance of our `CoffeeMachineState` interface. 
We initially set `Off` implementation of `State` when starting our coffee machine. Then, our implementations 
transition the state, based on different actions being performed.

{{<highlight kotlin "linenos=table">}}
package patterns.state

class CoffeeMachine {
    var state: CoffeeMachineState
    val MAX_BEANS_QUANTITY = 100
    val MAX_WATER_QUANTITY = 100
    var beansQuantity = 0
    var waterQuantity = 0
    val offState = Off(this)
    val noIngredients = NoIngredients(this)
    val ready = Ready(this)

    init {
        state = offState
    }

    fun turnOn() = state.turnOn()
    fun fillInBeans(quantity: Int) = state.fillInBeans(quantity)
    fun fillInWater(quantity: Int) = state.fillInWater(quantity)
    fun makeCoffee() = state.makeCoffee()
    fun turnOff() = state.turnOff()

    override fun toString(): String {
        return """COFFEE MACHINE → ${state::class.simpleName}
        |   water quantity : $waterQuantity
        |   beans quantity : $beansQuantity
        |""".trimMargin()
    }
}

abstract class CoffeeMachineState(val coffeeMachine: CoffeeMachine) {
    open fun makeCoffee(): Unit = throw UnsupportedOperationException("Operation not supported")
    open fun fillInBeans(quantity: Int): Unit = throw UnsupportedOperationException("Operation not supported")
    open fun fillInWater(quantity: Int): Unit = throw UnsupportedOperationException("Operation not supported")
    open fun turnOn(): Unit = throw UnsupportedOperationException("Operation not supported")
    fun turnOff() {
        coffeeMachine.state = coffeeMachine.offState
    }
}

class Off(coffeeMachine: CoffeeMachine) : CoffeeMachineState(coffeeMachine) {
    override fun turnOn() {
        coffeeMachine.state = coffeeMachine.noIngredients
        println("Coffee machine turned on")
    }
}

class NoIngredients(coffeeMachine: CoffeeMachine) : CoffeeMachineState(coffeeMachine) {
    override fun fillInBeans(quantity: Int) {
        if ((coffeeMachine.beansQuantity + quantity) <= coffeeMachine.MAX_BEANS_QUANTITY) {
            coffeeMachine.beansQuantity += quantity
            println("Beans filled in")
            if (coffeeMachine.waterQuantity > 0) {
                coffeeMachine.state = coffeeMachine.ready
            }
        }
    }

    override fun fillInWater(quantity: Int) {
        if ((coffeeMachine.waterQuantity + quantity) <= coffeeMachine.MAX_WATER_QUANTITY) {
            coffeeMachine.waterQuantity += quantity
            println("Water filled in")
            if (coffeeMachine.beansQuantity > 0) {
                coffeeMachine.state = coffeeMachine.ready
            }
        }
    }
}

class Ready(coffeeMachine: CoffeeMachine) : CoffeeMachineState(coffeeMachine) {
    override fun makeCoffee() {
        coffeeMachine.beansQuantity--
        coffeeMachine.waterQuantity--
        println("Making coffee ... DONE")
        if (coffeeMachine.beansQuantity == 0 || coffeeMachine.waterQuantity == 0) {
            coffeeMachine.state = coffeeMachine.noIngredients
        }
    }
}

fun main(args: Array<String>) {
    val coffeeMachine = CoffeeMachine()
    coffeeMachine.turnOn()
    println(coffeeMachine)
    coffeeMachine.fillInBeans(2)
    println(coffeeMachine)
    coffeeMachine.fillInWater(2)
    println(coffeeMachine)
    coffeeMachine.makeCoffee()
    println(coffeeMachine)
    coffeeMachine.makeCoffee()
    println(coffeeMachine)
    coffeeMachine.turnOff()
    println(coffeeMachine)
}
{{</highlight>}}

