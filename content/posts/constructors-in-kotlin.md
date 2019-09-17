---
title: Constructors in kotlin
date: 2017-06-25T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: true
images:
tags:
  - kotlin
  - software development
  - featured
---

# Constructors in general

Constructors are regular functions that give a chance to developer to initialise state of class instance. 
They are completely optional, and it's perfectly valid to have this, although not so useful :smiley:: 
                     
{{<highlight kotlin>}}
class Person
{{</highlight>}}

Constructors in `Kotlin` behave very similar to the ones we have in `Java`. We have two types of 
constructors in `Kotlin`:

* Primary
* Secondary

# Primary constructors

Primary constructors give us a chance to initialise state of our instance with very clean syntax. 
An example of the class having primary constructor would be:

{{<highlight kotlin>}}
class PersonWithPrimaryConstructorOnly(val name: String, val age: Int) {
    override fun toString() = "{name : '$name', age: $age}"
}
{{</highlight>}}

Primary constructor sits right next to the class name itself. Having `val` or `var` next to primary 
constructor parameters basically transforms passed arguments to class properties. Similar, but more 
verbose, and less preferred way to accomplish the same, would look like:

{{<highlight kotlin>}}
class PersonWithPrimaryConstructorVerbose1(name: String, age: Int) {
    var name: String = name
    var age: Int = age
    override fun toString() = "{name : '$name', age: $age}"
}
{{</highlight>}}

or:

{{<highlight kotlin>}}
class PersonWithPrimaryConstructorVerbose2(name: String, age: Int) {
    var name: String
    var age: Int

    init {
        this.name = name
        this.age = age
    }
    override fun toString() = "{name : '$name', age: $age}"
}
{{</highlight>}}

In this case, primary constructor doesn't define it's parameters as `val` or `var`, but rather we 
do the mapping of passed argument to properties explicitly. We should definitely prefer the first option 
where the compiler generates this mappings for us.

## Constructor visibility

Visibility in `Kotlin` is `public` by default, which applies to the constructors as well. In case we want to change 
it to, let's say, `private` we can do it like this: 

{{<highlight kotlin>}}
class PersonWithPrimaryConstructorOnly private constructor(val name: String, val age: Int) {
    override fun toString() = "{name : '$name', age: $age}"
}
{{</highlight>}}

# Secondary constructors

Secondary constructors are any constructors defined that are not defined as primary constructors. An example 
would be: 

{{<highlight kotlin>}}
class PersonWithOnlySecondaryConstructor {
    private var name: String
    private var age: Int

    constructor(name: String) {
        this.name = name
        this.age = 0
    }

    constructor(name: String, age: Int) : this(name) {
        this.age = age
    }

    override fun toString() = "{name: '$name', age: $age}"
}
{{</highlight>}}

We've got two constructors here. The first one, with only one, `name` parameter, just sets the name and initiates `age` to `0`. 
The second one, with additional `age` argument calls the first one with: 

{{<highlight kotlin>}}
: this(name)
{{</highlight>}}

and additionally sets age property explicitly.

In case you liked this post – feel free to subscribe to get more interesting content coming soon.
