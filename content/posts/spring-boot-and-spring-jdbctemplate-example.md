---
title: Spring boot and Spring JdbcTemplate example
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
---

Let's build some simple example that demonstrate ease of `Spring Boot` use when building applications that need to connect to, in this 
case - `MySql` database. Final output will be single - standalone jar file that can be executed as `java -jar my-boot-artifact.jar` and 
will be able to process http requests. Application is intentionally made simple, there's no big architecture inside it - you can see 
database code inside Controller method, which you'd normally not use for real world projects.

Let's get started:

* Create directory my-sample-boot-app
* Inside it create `pom.xml` file with the following content

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.test</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>demo</name>
    <description>Demo project for Spring Boot</description>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.2.5.RELEASE</version>
        <relativePath/>
        <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.34</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

We're including `spring-boot-starter-parent` to get all the intelligent defaults usually required. In order to get `Spring MVC` controllers 
and database access related code (`Spring JDBC Template` in this case), we include `spring-boot-starter-web`, `spring-boot-starter-jdbc`, `mysql-connector-java`
dependencies.  In order to package application as standalone jar file, we're including `spring-boot-maven-plugin`. After that we can import this as project in your 
own `IDE`. You should create directory structure such as the one given below:

```
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── demo
│   │   │       ├── DemoApplication.java
│   │   │       └── web
│   │   │           ├── Person.java
│   │   │           └── PersonController.java
│   │   └── resources
│   │       ├── application.properties
│   │       ├── static
│   │       └── templates
│   └── test
│       └── java
│           └── demo
│               └── DemoApplicationTests.java
```

Having maven installed on your machine, you can navigate to the root of your directory, and issue: `mvn spring-boot:run` command, which should start your application, with 
running `Tomcat` server instance inside ready to accept `http` requests.

`DemoApplication` is application entry class (contains main method). It is annotated with `SpringBootApplication` which bootstraps application and tries to guess the 
intelligent defaults your app might want to use based on what dependencies you listed in `pom.xml` file.

Our request handler can be accessed via `http://localhost:8099/person?id=1`. It responds to `HTTP` request with `JSON` response. You can use either browser or any `HTTP` client, 
such as `CURL`. Our `Spring` `MVC` controller looks like this:

```java
package demo.web;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class PersonController {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @RequestMapping(value = &quot;/person&quot;)
    public
    @ResponseBody
    Person dailyStats(@RequestParam Integer id) {
        String query = &quot;SELECT first_name, last_name, age&quot; +
                &quot; from person where person.id = &quot; + id;

        return jdbcTemplate.queryForObject(query, (resultSet, i) -&gt; {
            return new Person(resultSet.getString(1), resultSet.getString(2), resultSet.getInt(3));
        });

    }
}
```

You can see that actual `JDBC` code is quite lightweight. Our example requires database table schema such as:

```sql
CREATE TABLE `person` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(30) NOT NULL,
  `last_name` varchar(40) NOT NULL,
  `age` int(11) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `unique_id` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=latin1
```

Once database is set up, we can run our application via already mentioned `mvn spring-boot:run` shell command.

Initial call takes a bit longer, while maven downloads required dependencies, and assembles `jar`, which is ran afterwards.

:exclamation: This project requires `Java 8 JDK`.

Source code is hosted @ [Github](https://github.com/dodalovic/spring-boot-jdbc-templates)

Until next time!