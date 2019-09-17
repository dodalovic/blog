---
title: Kotlin powered Spring Boot app consuming NBA Rest Api
date: 2017-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: true
images:
tags:
  - kotlin
  - software development
  - rest
  - spring
  - spring boot
---

Ladies and gents, once again I’m sharing some sample app, this time we are building sample Spring Boot app implemented
 in Kotlin, talking to http://data.nba.net/ API to help us get some interesting, NBA related data. 
 In this case we’re just listing NBA teams, but you extend this sample even further.

# Creating project skeleton

The easiest way to create project skeleton is to navigate directly to Spring Boot Initializer website and download
 pre-configured project. In case you like kotlin – you can choose it from drop down with supported languages. If you’re 
 using Intellij or Spring Tools Suite, you can achieve the same from within your IDE.

# Application entry point

`KotlinNbaApplicaiton.kt` is our application entry point, it’s just a standard kotlin class containing main method and
 being annotated with `@SpringBootApplication`.

{{<highlight kotlin>}}
package rs.dodalovic.nba
 
import org.springframework.boot.SpringApplication
import org.springframework.boot.autoconfigure.SpringBootApplication
 
@SpringBootApplication
class KotlinNbaApplication
 
fun main(args: Array<String>) {
    SpringApplication.run(KotlinNbaApplication::class.java, *args)
}
{{</highlight>}}

# Handling request with TeamController

Let’s start with application entry point, simple servlet named `TeamController`, which is in charge of handling 
`http GET` `/nba/teams` requests. Owing to compact kotlin syntax – we’re simply autowiring `TeamRepository` (responsible 
for fetching data from 3rd party API) dependency via primary constructor. Controller is annotated by `@RestController` 
which makes him request handler, and `@RequestMapping("/teams")` is narrowing URL paths he handles. Furthermore, 
we’re defining `getAll()` function that is just proxying call to `TeamRepository` to actually get some data. Kotlin 
helps us here with expression functions so that we have one-liner to implement `getAll()`.

{{<highlight kotlin>}}
package rs.dodalovic.nba.team
 
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController
 
@RestController
@RequestMapping("/teams")
class TeamController(val teamRepository: TeamRepository) {
 
    @RequestMapping
    fun getAll() = teamRepository.getAll()
}
{{</highlight>}}

# Fetching data from external API

In order to get data from NBA API, we’re using `TeamRepository` kotlin class:

{{<highlight kotlin>}}
package rs.dodalovic.nba.team
 
import org.springframework.stereotype.Repository
import org.springframework.web.client.RestTemplate
import rs.dodalovic.nba.model.Team
 
val oddTeams = listOf("World", "West", "USA Celebrities", "USA", "Shanghai", "Real Madrid", "Haifa", "East",
        "Canada Celebrities", "Buenos Aires", "Barcelona")
 
val allTeamsEndpoint = "http://data.nba.net/data/10s/prod/v1/2016/teams.json"
 
@Repository
class TeamRepository(val template: RestTemplate) {
    fun getAll() = template.getForObject(allTeamsEndpoint, Map::class.java).extractTeams().nbaOnly().map { it.toTeam() }
}
 
private fun Map<*, *>.extractTeams() = (this.get("league") as Map<String, *>).get("standard") as List<Map<String, String>>
 
private fun Map<String, String>.toTeam() = Team(get("teamId")!!, get("city")!!, get("nickname")!!)
 
private fun List<Map<String, String>>.nbaOnly() = filterNot { rs.dodalovic.nba.team.oddTeams.contains(it["city"]) }
{{</highlight>}}

`TeamRepository` itself is slightly more complex. It’s been given `RestTemplate` HTTP client coming from Spring 
(`RestTemplate` `@Bean` is configured in `Config` `@Configuration` class). We need that one in order to fetch some 
data from NBA API we’re talking to. NBA API for some reasons return non NBA teams, so we build oddTeams collection. 
We’re also defining `allTeamsEndpoint` constant, which is our 3rd party data-source namespace.

# Kotlin extension functions

Implementation of `TeamRepository::getAll()` illustrates kotlin’s extension functions power to help us express ourselves
 in more readable, less cryptic manner. We’re first calling `RestTemplate::getForObject` API, which is first deserializing
  JSON API response to regular `Map`, then extracting teams portion out of response, proceeding with filtering only 
  NBA teams, and finishing by converting each team (which is `Map` instance) to `Team` model.

`extractTeam()`, `toTeam()` and `nbaOnly()` extension methods are defined below `TeamRepository` implementation. 
There’s nothing so special about them, they’re just dealing with `Map` transformations and `Collection` filtering.

# Running application

The easiest way to run the app is going to project root and executing `maven` command:

{{<highlight bash>}}
$ ./mvnw spring-boot:run
{{</highlight>}}

This will start application on port `8080`, and all application paths will be living under http://localhost:8080/nba 
namespace.

# Testing application

We’ll be using `CURL` client to talk to our API and get some NBA data:

{{<highlight bash>}}
$ curl -s localhost:8080/nba/teams | jq .
{{</highlight>}}

We’re piping response to `jq` `JSON` processor, just for sake of pretty printing response data.

Response will look something like:

{{<highlight json>}}
[{
"teamId": "1610612737",
"city": "Atlanta",
"nickname": "Hawks"
}, {
"teamId": "1610612738",
"city": "Boston",
"nickname": "Celtics"
}, {
"teamId": "1610612751",
"city": "Brooklyn",
"nickname": "Nets"
}, {
"teamId": "1610612766",
"city": "Charlotte",
"nickname": "Hornets"
}, {
"teamId": "1610612741",
"city": "Chicago",
"nickname": "Bulls"
}, {
"teamId": "1610612739",
"city": "Cleveland",
"nickname": "Cavaliers"
}, {
"teamId": "1610612742",
"city": "Dallas",
"nickname": "Mavericks"
}]
{{</highlight>}}

Sources can be found at [GitHub](https://github.com/dodalovic/kotlin-nba)

Until next time!