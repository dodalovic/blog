---
title: Spring MVC JUnit testing
date: 2018-10-28T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - software development
  - java
  - testing
  - spring
---

If you're interested in `JUnit` testing your `Spring` `MVC` controllers, feel free to visit `Github` [repo](https://github.com/dodalovic/mvc-controller-testing) 
containing very simple example of `Spring` `MVC` controller and `JUnit` test class testing it's endpoints. Project is implemented 
using `Spring Boot` framework.

The controller part looks like this:

```java
package rs.dodalovic.demos.category;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.net.URI;
import java.net.URISyntaxException;
import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/categories")
public class CategoriesController {

    private CategoryService categoryService;

    @RequestMapping
    public List<String> allCategories() {
        return categoryService.getAllCategories();
    }

    @RequestMapping(value = "/{categoryId}", method = RequestMethod.GET)
    public ResponseEntity<String> showCategory(@PathVariable("categoryId") String categoryId) {
        final Optional<String> category = categoryService.getCategory(categoryId);
        if (category.isPresent()) {
            return ResponseEntity.ok(category.get());
        }
        return ResponseEntity.badRequest().body(categoryId);
    }

    @RequestMapping(method = RequestMethod.POST, consumes = MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<Void> createCategory(@RequestBody Category category) throws
            URISyntaxException {
        return ResponseEntity.created(new URI("http://localhost/categories/1")).body(null);
    }

    @Autowired
    public CategoriesController(CategoryService categoryService) {
        this.categoryService = categoryService;
    }
}
```

There's no special functionality, it uses `CategoryService` to fetch some data. Also, exposes `http` endpoints (`showCategory`, 
`createCategory`) we'll exercise in our test class, which is given below:

```java
package rs.dodalovic.demos.category;

import org.hamcrest.core.StringContains;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.SpringApplicationConfiguration;
import org.springframework.http.MediaType;
import org.springframework.mock.web.MockServletContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import rs.dodalovic.demos.DemoApplication;

import java.util.Optional;

import static java.util.Arrays.asList;
import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.mock;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = DemoApplication.class)
@ContextConfiguration(classes = MockServletContext.class)
@WebAppConfiguration
public class CategoriesControllerTest {
    private MockMvc mockMvc;
    private CategoryService categoryService;

    @Before
    public void beforeEachTest() {
        categoryService = mock(CategoryService.class);
        given(categoryService.getAllCategories()).willReturn(asList("Category 1", "Category 2", "Category 3"));
        mockMvc = MockMvcBuilders.standaloneSetup(new CategoriesController(categoryService)).build();
    }

    @Test
    public void allCategories() throws Exception {
        mockMvc.perform(
                MockMvcRequestBuilders.get("/categories")
                        .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().string("[\"Category 1\",\"Category 2\",\"Category 3\"]"));
    }

    @Test
    public void asserts404ForNotExistingCategory() throws Exception {
        given(categoryService.getCategory("-1")).willReturn(Optional.empty());
        mockMvc.perform(
                MockMvcRequestBuilders.get("/categories/-1")
                        .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().is4xxClientError());
    }

    @Test
    public void assertsCategoryFound() throws Exception {
        given(categoryService.getCategory("1")).willReturn(Optional.of("Category 1"));
        mockMvc.perform(
                MockMvcRequestBuilders.get("/categories/1")
                        .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().string("Category 1"));
    }

    @Test
    public void postForbiddenToGetCategory() throws Exception {
        given(categoryService.getCategory("1")).willReturn(Optional.of("Category 1"));
        mockMvc.perform(
                MockMvcRequestBuilders.post("/categories/1")
                        .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isMethodNotAllowed());
    }

    @Test
    public void createsCategory() throws Exception {
        mockMvc.perform(MockMvcRequestBuilders.post("/categories")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"name\":\"Category\"}")
        ).andExpect(status().isCreated())
                .andExpect(header().string("location", new StringContains("/categories/")));
    }
}
```

For those familiar with `JUnit` testing concept this should not be hard to understand. For the beginners - I suggest reading 
my earlier [post](https://odalinjo.wordpress.com/2016/03/13/junit-testing-part-i-setup-with-simple-example/). Basic idea is to 
mock dependency our controller class has, performing `HTTP` call, and asserting `HTTP` status, body, headers etc...

Once more, Github repo with [sources](https://github.com/dodalovic/mvc-controller-testing).

Until next time!