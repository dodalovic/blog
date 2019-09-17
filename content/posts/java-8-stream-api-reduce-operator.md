---
title: Java 8 Stream API - reduce operator
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - software development
  - java
  - streams
---

Right after blog post covering `Stream.flatMap()` operator, we'll also cover another important `Stream` operator called - reduce. We'll go straight away with an example.

Say we have a simple `Person` `Java Bean`:

```java
package rs.dodalovic.streams.reduce;

class Person {
    private String name;
    private Gender gender;
    private Integer age;

    public Person(String name, Gender gender, Integer age) {
        this.name = name;
        this.gender = gender;
        this.age = age;
    }

    static enum Gender {
        MALE, FEMALE;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Gender getGender() {
        return gender;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", gender=" + gender +
                '}';
    }
}
```

Our task is to iterate through list of persons and find:

* Female person with the longest name and print the details
* Longest female name only and print the details
* Youngest male and print his/her age

Here's our reduce operator showcase right below:

```java
package rs.dodalovic.streams.reduce;

import java.util.Arrays;
import java.util.List;
import java.util.Optional;
import java.util.OptionalInt;
import java.util.function.Predicate;

public class ReduceDemo {
    public static void main(String[] args) {

        final List<Person> persons = Arrays.asList(
                new Person("Jeanny", Person.Gender.FEMALE, 22),
                new Person("Jim", Person.Gender.MALE, 25),
                new Person("Alicia", Person.Gender.FEMALE, 32),
                new Person("Jennifer", Person.Gender.FEMALE, 38),
                new Person("Anastasia", Person.Gender.FEMALE, 19),
                new Person("John", Person.Gender.MALE, 22),
                new Person("Jimmy", Person.Gender.MALE, 38),
                new Person("Michael", Person.Gender.MALE, 15)
        );
        printFemaleWithLongestName(persons);
        printLongestFemaleNameOnly(persons);
        printYoungestMaleAge(persons);
        printYoungestMaleAgeUsingMin(persons);
    }

    private static void printFemaleWithLongestName(List<Person> persons) {
        final Optional<Person> personWithLongestName = persons.stream()
                .filter(onlyGender(Person.Gender.FEMALE))
                .reduce((person, person2) -> person.getName().length() > person2.getName().length() ? person : person2);
        personWithLongestName.ifPresent(person -> System.out.printf("Person with longest female name %s%n",
                personWithLongestName));
    }

    private static void printLongestFemaleNameOnly(List<Person> persons) {
        persons.stream()
                .filter(onlyGender(Person.Gender.FEMALE))
                .map(Person::getName)
                .reduce((person1Name, person2Name) -> person1Name.length() > person2Name.length() ? person1Name :
                        person2Name)
                .ifPresent(longestName -> System.out.printf("Longest female name: %s%n", longestName));
    }

    private static void printYoungestMaleAge(List<Person> persons) {
        final Integer youngestMaleAge = persons.stream()
                .filter(onlyGender(Person.Gender.MALE))
                .map(Person::getAge)
                .reduce(Integer.MAX_VALUE, (age1, age2) -> age1 < age2 ? age1 : age2);
        System.out.printf("Youngest male age: %d%n", youngestMaleAge);
    }

    private static void printYoungestMaleAgeUsingMin(List<Person> persons) {
        final OptionalInt youngest = persons.stream()
                .filter(onlyGender(Person.Gender.MALE))
                .mapToInt(Person::getAge)
                .min();
        youngest.ifPresent(value -> System.out.printf("Youngest male age: %d", value));
    }

    private static Predicate<Person> onlyGender(Person.Gender gender) {
        return person -> person.getGender() == gender;
    }
}
```

First - we initialize our sample person list that we'll operate on. To find the person with the longest name, we call `printFemaleWithLongestName()` method. What we do there is first taking stream out of list
 (call to `stream()` method), followed by filtering stream to keep `Gender.FEMALE` persons only and then calling our `reduce()` method. The parameter it receives is `BinaryOperator<T, T, T>`, which is 
 basically a `Function` receiving two (same type) parameters, and returning value of that same type. This `Function` will be called either:
 
* `n-1` times, where `n` is number of elements in underlying collection, in case we didn't supply initial / default value to `reduce()` call
* `n` times otherwise

So, in case we have collection of elements `A`, `B`, `C`, `D` - function will be called `3` times (if we haven't supplied initial value to `reduce()`) with parameters:

* the first call will be with `A`, `B` elements of collection
* the second call will take result of previous function call as the first value, and `C` as the second
* similarly, the third call will take result of second function call as the first value, and `D` as the second

If we passed the initial/default value, function would have been called `4` times, with the only difference that the first call will have that initial/default value as the first parameter, and `A` as the 
second one. Afterwards algorithm stays the same.

In case of `printFemaleWithLongestName()` function, for each pair of persons combinations we ask:

```java
(person, person2) -> person.getName().length() > person2.getName().length() ? person : person2
```

In case `person`'s name is longer than person2`'s - we return the first person, otherwise - person2`. So, after traversing each two pairs of persons, we'll at the end have as a result of call to `reduce()`
 - `Optional`.

Returning `Optional` is fine since it might be the case that list we're streaming through might be empty one, so we can't actually get the person with longest name there. That's why, in this case, we 
call `ifPresent()` at the end - passing it `Consumer` to do with our `Person` object whatever we like.

The other example, `printLongestFemaleNameOnly` , only differs that we're calling `map()` to transform `Person` instances to `Person` instance name field. After that we similarly call reduce operator, 
comparing lengths in there, and the same as the first time, if underlying collection wasn't empty, we extract from resulting `Optional` value, which represents longest female name in given list.

In case of `printYoungestMaleAge` method, we're first filter to keep males only by passing `Predicate` `onlyGender(Person.Gender.FEMALE)` to `filter()` call. Afterwards, we're transform underlying 
list ato convert to list of person ages by calling `map()` passing `Person::getAge` conversion function to it. After that we're calling `reduce()` function with slightly overloaded signature. 
This one accepts initial / default value as a first parameter, and `BinaryOperator` as second one. If underlying collection is empty, `reduce()` will return initial/default value passed to
 it (`Integer.MAX_VALUE`), otherwise - similarly what we had in our previous cases, we iterate through all pairs of Persons and return finally the one that's the youngest. The only difference, 
 in this case is that if underlying collection is `empty()`, we won't return empty `Optional` but rather default value we passed as the first parameter to `reduce()` function.

Another, slightly easier way to achieve minimum age calculation is to use `min()` terminal operator on `IntStream`. This is demonstrated in `printYoungestMaleAgeUsingMin` method. The only differences 
are that instead of calling `map()`, we call `mapToInt()` which converts existing stream to `IntStream`, which is convenient since it has methods such as `sum()`, `min()`, `max()`. 

Source code as Github [gist](https://gist.github.com/dodalovic/50aafc5d9ff65255e314)

Key takeaways

`Reduce` operator is terminal operator that helps us extract single value from underlying stream. Accumulator function is called either `n-1` times for collection of n elements if we haven't provided 
initial / default value as the first parameter, or `n` times - if we did (see algorithm description above).

Stay tuned and please – don’t forget to subscribe in case you’re eager to find out what’s coming next in upcoming posts.