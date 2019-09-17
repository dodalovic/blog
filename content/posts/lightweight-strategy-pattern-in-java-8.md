---
title: Lightweight strategy pattern in Java 8
date: 2018-08-28T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - software development
  - software patterns
  - java
---

> In computer programming, the strategy pattern (also known as the policy pattern) is a software design pattern that enables an 
>algorithm's behavior to be selected at runtime. The strategy pattern. defines a family of algorithms, encapsulates each algorithm, 
>and. makes the algorithms interchangeable within that family.

Let's start straight away with an example. Task is to build an application that would load a user information from arbitrary storage, 
log user details using arbitrary logger and finally - save user using different persistence options. App should be a `Java` console 
application receiving input as command line parameters.

Say we have a main class:

```java
package rs.dodalovic.design_patterns.behavioral.strategy.user_persistence;

import com.google.common.collect.ImmutableMap;

import java.util.Map;
import java.util.function.Consumer;
import java.util.function.Function;

public class UserPersistMain {

    private static final Map<String, Function<Integer, User>> userProviderMapping = ImmutableMap.of(
            "fs-provider", UserProviders.FILE_SYSTEM,
            "web-service-provider", UserProviders.WEB_SERVICE
    );

    private static final Map<String, Consumer> userPersisterMapping = ImmutableMap.of(
            "mongo-persister", UserPersisters.MONGO,
            "mysql-persister", UserPersisters.MYSQL
    );

    private static final Map<String, Consumer> userLoggerMapping = ImmutableMap.of(
            "console-logger", UserLoggers.CONSOLE,
            "sentry-logger", UserLoggers.SENTRY
    );

    public static void main(String[] args) {

        int userId = Integer.parseInt(args[0]);
        final Function<Integer, User> userProvider = userProviderMapping.get(args[1]);
        final Consumer userPersister = userPersisterMapping.get(args[2]);
        final Consumer userLogger = args.length == 4 ? userLoggerMapping.get(args[3]) : UserLoggers.CONSOLE;

        new UserPersistenceDirector(userId, userProvider, userPersister, userLogger).persist();
    }
}
```

The first argument passed from command line is `userId`. The second one is `userProvider`, the next one is `userPersister`, and 
finally, let's say we can optionally pass `userLogger` (in case we don't provide it via command line input, we default to console 
logging).

We have provided mappings from command line parameter names to different implementations (strategies) as static maps: 
`userProviderMapping`, `userPersisterMapping`, `userLoggerMapping`. Depending on values passed from command line, we will choose 
appropriate implementation at program runtime.

Strategies implementations are encapsulated withing separate interfaces. `UserProviders` contains implementations of various strategies 
for loading user details. `UserPersisters` interface contains implementations of different strategies that are taking care of 
persisting user details. `UserLoggers` contains different strategies for logging user details. 

These strategies are quite lightweight, since we're using `Java 8` concepts of functional interfaces that come handy in our case. 
Given below are our interfaces containing strategies:

```java
package rs.dodalovic.design_patterns.behavioral.strategy.user_persistence;
import java.util.function.Function;

interface UserProviders {
    Function<Integer, User> FILE_SYSTEM = userId -> {
        System.out.println("Retrieving user data from file system...");
        return new User(userId, "Mike", "United States");
    };
    Function<Integer, User> WEB_SERVICE = userId -> {
        System.out.println("Retrieving user data from web service...");
        return new User(userId, "Jane", "Canada");
    };
}
```

```java
package rs.dodalovic.design_patterns.behavioral.strategy.user_persistence;
import java.util.function.Consumer;

interface UserPersisters {
    Consumer MONGO = user -> System.out.format("Persisting user [%s] to Mongo DB...%n", user.toString());
    Consumer MYSQL = user -> System.out.format("Persisting user [%s] to MySQL DB...%n", user.toString());
}
```

```java
package rs.dodalovic.design_patterns.behavioral.strategy.user_persistence;
import java.util.function.Consumer;

interface UserLoggers {
    Consumer CONSOLE = user -> System.out.format("Logging user [%s] to console...%n", user.toString());
    Consumer SENTRY = user -> System.out.format("Logging user [%s] to sentry...%n", user.toString());
}
```

Also, we have a simple `Java Bean`:

```java
package rs.dodalovic.design_patterns.behavioral.strategy.user_persistence;

class User {
    private final int id;
    private final String username;
    private final String location;

    public User(int id, String username, String location) {
        this.id = id;
        this.username = username;
        this.location = location;
    }

    @Override
    public String toString() {
        return String.format("User{id=%d, username='%s', location='%s'}", id, username, location);
    }
}
```

Important class in our case is `UserPersistenceDirector` which takes care of entire process: loading, logging and finally - persisting 
user. It's quite lightweight, since it just delegates persisting process steps to strategies we provide at runtime. Let's see the 
implementation:

```java
package rs.dodalovic.design_patterns.behavioral.strategy.user_persistence;

import java.util.function.Consumer;
import java.util.function.Function;

class UserPersistenceDirector {

    private final int userId;
    private final Function<Integer, User> userProvider;
    private final Consumer userPersister;
    private Consumer userLogger;

    public UserPersistenceDirector(int userId, Function<Integer, User> userProvider, Consumer userPersister) {
        this.userId = userId;
        this.userProvider = userProvider;
        this.userPersister = userPersister;
    }

    public UserPersistenceDirector(int userId, Function<Integer, User> userProvider, Consumer userPersister,
                                   Consumer userLogger) {
        this(userId, userProvider, userPersister);
        this.userLogger = userLogger;
    }

    public void persist() {
        final User user = userProvider.apply(userId);
        userLogger.accept(user);
        userPersister.accept(user);
    }
}
```

We can see that it has constructors receiving strategies. We inject strategies at runtime, depending on input parameters. We store 
strategies as instance fields. `UserPersistenceDirector` has a `public` method `persist()` , which coordinates high level persistence 
mechanism. We can see in it's implementation that all it does is delegating job to strategies that were given to it. So, this is a 
high level component that defines process and it's steps, and we can completely vary algorithm passing different strategies at 
runtime. So, client of `UserPersistenceDirector` has complete control of picking the strategies it wants executed.

Advantage of using `Java 8` is that it ships with functional interfaces, such as `Consumer<T>`, `Function<T,R>` which we can use for 
defining strategies and we can use elegant `lambda` syntax to provide strategies implementations.

Conclusion: whenever we want the ability to execute some high level process that has well established steps, but we want to be able 
to control & vary implementation of these steps - `Strategy pattern` comes handy.

We can execute our application giving sample input parameters. If we want to pass `userId:1` , user provider as `fs-provider`,  
user persister as `mongo-persister`, and user logger as  `sentry-logger`, we can execute:

```shell script
java rs.dodalovic.design_patterns.behavioral.strategy.user_persistence.UserPersistMain 1 fs-provider mongo-persister sentry-logger
Output:
Retrieving user data from file system...
Logging user [User{id=1, username='Mike', location='United States'}] to sentry...
Persisting user [User{id=1, username='Mike', location='United States'}] to Mongo DB...
```

Hope you liked the post. You can find sources at [Github](https://gist.github.com/dodalovic/99aff2a2e6407f196204)

Until next time!