---
title: Java 8 flatMap stream operator
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
---

Let's see a simple example of `Java 8` `Stream::flatMap` method found in the `Stream` API. Definition of `flatMap` in the `API` doc is:

> Returns a stream consisting of the results of replacing each element of this stream with the contents of a mapped stream produced by 
>applying the provided mapping function to each element. Each mapped stream is closed after its contents have been placed into this 
>stream. (If a mapped stream is null an empty stream is used, instead.)

So - say we have a simple `Java` class named `Developer`:

```java
package rs.dodalovic.streams.flap_map;

import java.util.List;

public class Developer {
    private final String name;
    private final List<String> programmingLanguages;

    public Developer(String name, List<String> programmingLanguages) {
        this.name = name;
        this.programmingLanguages = programmingLanguages;
    }

    public String getName() {
        return name;
    }

    public List<String> getProgrammingLanguages() {
        return programmingLanguages;
    }
}
```

Simple `POJO` contains the developer name, as well as list of programming languages programmer knows. Now, let's create some 
executable code to finally demo what we want:

```java
package rs.dodalovic.streams.flap_map;

import java.util.List;
import java.util.Set;
import java.util.function.Function;
import java.util.stream.Collectors;
import java.util.stream.Stream;

import static java.util.Arrays.asList;

public class FlatMapMain {

    private static final String JAVA = "Java";
    private static final String PHP = "Php";
    private static final String JAVASCRIPT = "Javascript";
    private static final String RUBY = "Ruby";
    private static final String SCALA = "Scala";
    private static final String PYTHON = "Python";
    private static final String CLOJURE = "Clojure";

    public static void main(String[] args) {

        final List<Developer> developers = asList(
                new Developer("John", asList(JAVA, PHP)),
                new Developer("Peter", asList(JAVA, PHP, JAVASCRIPT)),
                new Developer("Mike", asList(JAVA, PHP, CLOJURE)),
                new Developer("Steven", asList(JAVA, PHP, JAVASCRIPT, PYTHON)),
                new Developer("Kylie", asList(PHP, RUBY, SCALA)));

        final Set<String> supportedProgrammingLanguages = developers.stream()
                .flatMap(toLanguagesStream())
                .collect(Collectors.toSet());

        System.out.format("All supported languages: \n\n");
        supportedProgrammingLanguages.forEach(System.out::println);
    }

    private static Function<Developer, Stream<String>> toLanguagesStream() {
        return developer -> developer.getProgrammingLanguages().stream();
    }
}
```

First, we populate developers List with some sample developers. What we do after is that we start streaming through given developers, 
and then want to collect all the languages that all given developers support in total. We do that using `flatMap` operator which accepts 
mapping `Function` parameter. In our case we implemented the mapping in `toLanguagesStream` method, where we map each developer to it's 
supported languages stream. What happens then is that for each developer we get it's languages, which are merged at the end - after 
last developer is streamed. `Java Stream API` will do the merging for us, so `flatMap` returns final stream - consisting of all merged 
languages for all the developers.

In order to display only distinct languages (since we have the cases one language is shared across many developers) we use collect 
operator, which accepts instance of `Collector` interface. In our case we choose out of the box implementation which is shipped with 
`JDK - Collectors.toSet()`.

Code snippets can be seen [here](https://gist.github.com/dodalovic/571f1730774ccae6d949)

Feel free to experiment with `flatMap` and find other useful cases to apply it.

Stay tuned and please – don’t forget to subscribe in case you’re eager to find out what’s coming next in upcoming posts.