---
title: What is unit testing?
date: 2019-09-16T22:54:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - testing
  - featured
---

For all of you that are just new to the topic, I'll try to make a concise introduction to the idea of unit testing. 

Unit testing allows us to test our functions in isolation, so that we can verify implementation correctness. 

In general, we should plan our test method implementation to be divided in three parts: 

{{< figure src="/posts/img/testing-tripple-a.jpeg" position="center" style="border-radius: 8px;" caption="Tripple A in unit testing" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

## Arrange phase

In this phase we "emulate" execution environment of the function we are testing. What does that mean? Let's use `Kotlin` snippet of 
the function we want to unit test:  

```kotlin
class ProductService(
    val shippingCostService: ShippingCostService, 
    val taxCalculationService: TaxCalculationService,
    val productPriceService: PriceService) { 

    fun getPrice(productID: String): Long {
        val basePrice: Long = priceService.getPrice(productID)
        val shippingCosts: Long = shippingCostService.getShippingCosts(productID)
        val taxes: Long = taxCalculationService.calculateTax(productID)
        return basePrice + shippingCosts + taxes 
   }
}
```
Here we have imaginary `ProductService` with `getPrice` method, which we'd like to test. Unfortunately - 
there are already some complications: this method can't be tested in isolation! Why? Simply because our class 
depends on other classes to fulfill it's responsibility: `ShippingCostService`, `TaxCalculationService` and 
`PriceService`. We can call them collaborators. 

Luckily - all modern programming languages support some kind of support for emulation of our collaborators. 
Using these tools, we can give instructions to our test engine to emulate their particular behavior during test 
method execution. 

{{< figure src="/posts/img/when-foo-then-bar.jpeg" position="center" style="border-radius: 8px;" caption="Emulate your environment!" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

In our imaginary case - we could give such an instruction: 

```kotlin
class TestClass {
    @Test
    fun getPrice_when_shipping_cost_service_returns_proper_number_returns_positive_number() {
        // arrange phase
        val productID = UUID.randomUUID().toString()       
        when(priceService.getPrice(productID)).thenReturn(5L) 
        ...
    }
}
```

## Act phase

{{< figure src="/posts/img/tests-act.png" position="center" style="border-radius: 8px;" caption="Execute your function!" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

In this phase we actually call the function we want to test:

```kotlin
class ProductServiceTest {
    @Test
    fun myTestMethod() {
        // act phase
        val calculatedPrice = productService.getPrice("XY-123")
        // assert phase
        ...
    }
}
```
  
## Assert phase

{{< figure src="/posts/img/tests-always-verify.png" position="center" style="border-radius: 8px;" caption="Never forget verifying!" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

In this phase, we may want to do some of the following things:

* verify return value from the function we tested
  ```kotlin
  ...
  assertThat(calculatedPrice).isEqualTo(5)
  ...
  ``` 
* verify that we had proper interactions with our collaborators during our test execution
  ```kotlin
  ...
  verify(taxCalculationService, times(1)).calculateTax(productID)
  ...
  ``` 

## Naming tests methods 

One of the "patterns" I use when naming my test methods is something like: 

> `getPrice_when_shipping_cost_service_returns_proper_number_returns_positive_number`

It starts with the method name we test, `getPrice`, continues with the description of emulated execution environment, 
`_when_shipping_cost_service_returns_proper_number` in this case, and ends with expected outcome - `returns_positive_number`

## Things to remember

- :heavy_check_m Each test method should be composed out of three code blocks - **Arrange**, **Act**, and **Assert**
- :heavy_check_m We should name our test methods so that it's enough to understand the test just be reading test method 
name
- :heavy_check_m We should have multiple scenarios where we execute our method, with various emulated environments which 
our function can exhibit during regular application usage

Until next time! 
