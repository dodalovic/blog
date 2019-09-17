---
title: Asynchronous event publishing using Spring Boot
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - java
  - spring
  - spring boot
  - featured
---

I’ve created a sample `Spring Boot` app that demonstrates useful scenario of asynchronous publish / subscribe model. It could be 
useful in many ways. Idea is that, for example, you have some `API` handling some web requests, and apart from functionality it provides, it also 
publishes some event, so that some other thread can react upon it, with main functionality still being processed with request handling thread. 
Or even shorter – request handling thread will do it’s job, and publishes some event (using `Java` annotation), and some other background thread 
will subscribe to event and process it. You can use it for scenario you register user with your `API`, and publish user registered event, and in 
background you can subscribe to it by sending out e-mail to end user.


Easiest way to start your application could be running it via `spring-boot:run` `Maven` goal:

```shell script
mvn spring-boot:run
```

That will start our demo app on standard `8080` port.

Demo app contains two `API`s, get user and update user. Those are just dummy implementations. It also has a `User` domain model class.

You can then, using command line and `curl` tool, issue the get user request (I’m just doing pipe to `jq`[^1] tool  for nicer response rendering – 
you can omit that part):

```shell script
curl -s 'http://localhost:8080/users/1' | jq .
{
 "id": 1,
 "age": 33,
 "name": "Some name"
}
```

Application code will show something like:

```shell script
2017-04-23 11:46:59.940 INFO 7421 --- [nio-8080-exec-1] de.odalinho.async.PublishingController : PublishingController::Thread.currentThread().getName() = http-nio-8080-exec-1
2017-04-23 11:46:59.941 INFO 7421 --- [pool-1-thread-3] d.o.a.m.UserNotificationPublisher : UserNotificationPublisher::Thread.currentThread().getName() = pool-1-thread-3
2017-04-23 11:46:59.941 INFO 7421 --- [pool-1-thread-3] d.o.a.m.UserNotificationPublisher : UserNotificationPublisher::event.getMessage() = User(id=1, age=33, name=Some name)
```

You can see that `Tomcat`’s request handling thread named `http-nio-8080-exec-1` is handling web request, whereas `pool-1-thread-3` is handling background event processing.
For the remaining, update user `API` call, similar to the first one, we’re issuing command:

```shell script
curl -s \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
http://localhost:8080/users/1 \
-d '{"id":1,"age":25,"name":"updated-name"}' | jq .
{
 "id": 1,
 "age": 25,
 "name": "updated-name"
}
```

Application log will show something similar to:

```shell script
2017-04-23 11:47:17.747 INFO 7421 --- [nio-8080-exec-2] de.odalinho.async.PublishingController : PublishingController::Thread.currentThread().getName() = http-nio-8080-exec-2
2017-04-23 11:47:17.749 INFO 7421 --- [pool-1-thread-3] d.o.a.m.UserNotificationPublisher : UserNotificationPublisher::Thread.currentThread().getName() = pool-1-thread-3
2017-04-23 11:47:17.749 INFO 7421 --- [pool-1-thread-3] d.o.a.m.UserNotificationPublisher : UserNotificationPublisher::event.getMessage() = User(id=1, age=25, name=updated-name)
```

From the application standpoint, you need `PublishingConfiguration` config class to configure `Spring`’s interceptor and background processing. I’m creating thread pool 
of size 5 for background processing purposes.

In `de.odalinho.async.messaging` package there’s all required infrastructure to support publish / subscribe functionality. `PublishEventAdvisor` component is `Spring`’s 
`AOP` component, which is basically just a mapper saying we would like to intercept `PublishEvent` annotation annotated methods using `PublishEventAnnotationProcessor` 
interceptor class. `PublishEventAnnotationProcessor` uses reflection to fulfill all this magic functionality.

There’s also `PublishingController` that is simple dummy `API` to demonstrate functionality:

```java
package de.odalinho.async;
 
import de.odalinho.async.messaging.PublishEvent;
import de.odalinho.async.model.User;
import lombok.extern.slf4j.Slf4j;
import lombok.val;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;
 
import static de.odalinho.async.messaging.Event.USER_CREATED;
import static de.odalinho.async.messaging.Event.USER_UPDATED;
 
@RestController
@Slf4j
public class PublishingController {
 
    @PublishEvent(event = USER_CREATED)
    @GetMapping("/users/{userId}")
    User getUser(@PathVariable int userId) {
        val user = new User().setId(userId).setAge(33).setName("Some name");
        log.info("PublishingController::Thread.currentThread().getName() = " + Thread.currentThread().getName());
        return user;
    }
 
    @PublishEvent(event = USER_UPDATED)
    @PostMapping(value = "/users/{userId}", consumes = MediaType.APPLICATION_JSON_VALUE, produces = MediaType.APPLICATION_JSON_VALUE)
    User updateUser(@PathVariable int userId, @RequestBody User user) {
        log.info("PublishingController::Thread.currentThread().getName() = " + Thread.currentThread().getName());
        return user;
    }
}
```

You could notice these `API` methods are annotated with` @PublishEvent(event = USER_CREATED)`. Framework will execute this method and return value of 
method (`User` model) will be published and available to `UserNotificationPublisher`, which is background component handling all events in general. 
It receives `UserNotificationEvent` which contains `getMessage()` `API` which returns our `User` model.

You can extend this by creating new, custom `Events` and annotating your `API` methods with `@PublishEvent` annotation with your new `Event` type.

Demo app is using `Lombok` library[^2] to get rid of some boilerplate Java code required.

You can checkout source code from [GitHub](https://github.com/dodalovic/async-publish)

Hope you liked this post. If so – don’t hesitate to subscribe!

[^1]: https://stedolan.github.io/jq/
[^2]: https://projectlombok.org