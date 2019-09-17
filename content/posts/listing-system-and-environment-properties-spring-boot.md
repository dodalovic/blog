---
title: Listing system and environment properties in Spring Boot
date: 2017-04-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - spring
  - spring boot
  - software development
---

Ladies & Gents, I prototyped just a small app that could help you print out some basic 
environment related information by exposing a few Rest controller endpoints.

# Running the app

The easiest way is to run the app using Spring boot maven plugin

{{<highlight bash>}}
$ mvn spring-boot:run \
  -Drun.jvmArguments="\
    -Dfoo=foo \
    -Dbar=bar \
    -Dbaz=baz \
    -Dserver.port=8080 \
    -agentlib:jdwp=transport=dt_socket,server=y,address=1044,suspend=n"
{{</highlight>}}

Here, we specify some `Java` properties that will be used by the application:

foo, bar, baz custom properties
server.port – which is the way to instruct `Spring Boot` which port to use
we also specify to start with remote debug capabilities (if we’d like to do some remote 
debugging with our IDE)

Rest controller exposing various system / spring properties

{{<highlight java>}}
package rs.dodalovic.envprops;

import com.google.common.collect.ImmutableMap;
import lombok.AllArgsConstructor;
import lombok.val;
import org.springframework.core.env.Environment;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

@RestController
@AllArgsConstructor
public class EnvPropertiesController {
    private final Environment environment;

    @GetMapping("/sys/props")
    public Map<String, Object> getSysProperties() {
        val builder = ImmutableMap.builder();
        System.getProperties().stringPropertyNames().forEach(name -> {
            builder.put(name, environment.getProperty(name));
        });
        val systemProperties = builder.build();
        return ImmutableMap.of(
                "size", systemProperties.size(),
                "values", systemProperties
        );
    }

    @GetMapping("/sys/props/custom")
    public Map<String, Object> getCustomEnvProperties() {
        val builder = ImmutableMap.builder();
        builder.put("properties.key1", environment.getProperty("properties.key1"));
        builder.put("properties.key2", environment.getProperty("properties.key2"));

        builder.put("foo", environment.getProperty("foo"));
        builder.put("bar", environment.getProperty("bar"));
        builder.put("baz", environment.getProperty("baz"));

        val customProperties = builder.build();

        return ImmutableMap.of(
                "size", customProperties.size(),
                "values", customProperties
        );
    }

    @GetMapping("/sys/env")
    public Map<String, Object> get() {
        val env = System.getenv();
        return ImmutableMap.of(
                "size", env.size(),
                "values", env
        );
    }
}
{{</highlight>}}

Highlighted are lines used to get insights from running application environment. We are injecting
 `Environment` implementation which gives us access to environment properties, and application
  profiles.

Here's how `application.properties` look like:

{{<highlight properties>}}
properties.key1=properties.val1
properties.key2=properties.val2
{{</highlight>}}

In the `application.properties` (main `Spring Boot` configuration file) we define two custom
 `Spring` environment properties: `properties.key1` and `properties.key2`.

# Listing system properties

{{<highlight bash>}}
$ curl -s 'http://localhost:8080/sys/props' | jq .
{{</highlight>}}

Here's a response:

{{<highlight json>}}
 {
   "size": 63,
   "values": {
     "java.runtime.name": "Java(TM) SE Runtime Environment",
     "sun.boot.library.path": "/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/jre/lib",
     "java.vm.version": "25.121-b13"
     // ...
 }
}
{{</highlight>}}

# Listing system environment

{{<highlight bash>}}
$ curl -s 'http://localhost:8080/sys/env' | jq .
{{</highlight>}}

Here's a response, once again:

{{<highlight json>}}
{
  "size": 46,
  "values": {
    "M2": "/usr/local/Cellar/maven/3.5.0/libexec/bin",
    "GREP_COLOR": "1;33",
    "JENV_FORCEJDKHOME": "true"
    // ...
  }
}
{{</highlight>}}


# Listing custom spring properties

{{<highlight json>}}
$ curl -s 'http://localhost:8080/sys/props/custom' | jq .
{{</highlight>}}

A response would be something like:

{{<highlight json>}}
{
  "size": 5,
  "values": {
    "properties.key1": "properties.val1",
    "properties.key2": "properties.val2",
    "foo": "foo",
    "bar": "bar",
    "baz": "baz"
  }
}
{{</highlight>}}

Here we see merged collection of environment properties we either passed via command line 
or defined in `application.properties`.

You can checkout source code from [GitHub](https://github.com/dodalovic/env-properties-demo)

Until next time!