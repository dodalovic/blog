---
title: Microservices with Spring Boot, Netflix Zuul, Kotlin, Docker and MongoDB
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
---

Recently I've started playing with Kotlin programming language, and wanted to give it a try. Kotlin gives us possibility to write quite concise code.

I've built sample, small micro-service, composed of some kind of simple `api-gateway` or `proxy` server, implemented using `Spring Boot` and [Netflix Zuul](https://github.com/Netflix/zuul) 
library for proxying requests to downstream servers.

The `api-gateway` which is public facing proxy server doesn't do more than just forwarding request to downstream `user-service micro-service`.

User service is implemented in `Spring Boot`, using `Kotlin` as a language of choice, having `MongoDB` as persistent storage. It has only two `APIs` - **create 
user** and **retrieve user**. I'm using command line to test these `APIs`. Idea is to call `api-gateway` that just downstream request to `user-service`. This kind 
of simulates some real-life scenarios of how you might organize your service oriented architectures.

`docker-compose` comes quite handy to start / shutdown all the services with simple commands and orchestrate them via `docker-compose.yaml` configuration file.

You can find and download source code from [GitHub](https://github.com/dodalovic/kotlin-microservices). Readme file there explains how to start & use services. 

Stay tuned and, if you liked this post, don't forget to subscribe for getting more interesting content!