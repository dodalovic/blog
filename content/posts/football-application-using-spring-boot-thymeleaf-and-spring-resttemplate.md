---
title: Football application using  Spring boot, Thymeleaf and Spring RestTemplate
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
---

As part of my effort to adopt `Spring Boot` I noticed that I need to learn new view technology in `Java` ecosystem, since `Spring Boot` doesn't promote `JSP`, which I was 
used to using. I've explored alternatives a bit, and decided to give a shot to `Thymeleaf` as templating engine.

I was exploring a bit what it offers, and seems that I'll stick to it in the future - based on set of nice features it has. I am providing you a `Spring Boot` application 
using the [Thymeleaf](https://www.thymeleaf.org) as a view technology so that you can take a look at it and see it if it suits your needs.

Additionally, to make it non trivial application, I've decided to demonstrate usage of Thymeleaf by building football (soccer) application that integrates with [free football 
api](http://api.football-data.org/index). Just go ahead and register quickly for free api token that your app can use to communicate to the external service. My application 
communicates with 3rd party using `Spring`'s `RestTemplate` `API`.

After registering with `API`, you'll get `API` key, which you need to pass it as `JVM` argument when starting application (see below).

In the upcoming posts I will give my best to present most important aspects of Thymeleaf itself. In a meanwhile, feel free to download application sources and take a look at it.

In order to run the application, you need to go to app's root directory and execute `Maven` command (make sure to exchange `ABCDEF` with your `API` key):


```shell script
spring-boot:run -Dsoccerapis_token=ABCDEF
```

which will start embedded `Tomcat` container running on `8080` port. After application is started, you can access it via:

```shell script
http://localhost:8080/
```

Application displays european soccer leagues. By choosing league, you can further drill down to teams in given league, and finally - by choosing particular league - you 
can see the players squad with player details.

You can download application sources at [Github](https://github.com/dodalovic/boot-soccer).

Until next time!