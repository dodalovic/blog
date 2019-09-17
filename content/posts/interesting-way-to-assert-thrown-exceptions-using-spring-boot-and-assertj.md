---
title: Interesting way to assert thrown exceptions using Spring Boot and Assertj
date: 2017-04-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - testing
  - software development
---

## Traditional exception handling in JUnit tests 

You’re probably used to testing code that throws exceptions in either one of the following two ways:

* `JUnit`'s `@Test` annotation `expected` parameter

    {{<highlight java>}}
    @Test(expected = UserService.NoSuchUserException.class)
    public void deletesExistingUser() {
        final User existingUser = spy(new User());
        given(userRepository.findOne(eq("123"))).willReturn(existingUser);
        given(userRepository.save(eq(existingUser))).willReturn(existingUser);
    
        userService.deleteUser("123");
    
        verify(existingUser).setState(eq(User.State.DELETED));
    }
    {{</highlight>}}
    
    The disadvantage of this approach is that we can't be sure that the exception thrown 
    was thrown by the code we expect to throw it

* JUnit’s `ExpectedException @Rule`:

    To gain control over which part of test should throw an exception, we can use `ExpectedException`
    `JUnit` rule, here's an example:

    {{<highlight java>}}
    @Rule
    ExpectedException thrown = ExpectedException.none();
     
    @Test
    public void deletesExistingUser() {
        thrown.expect(UserService.NoSuchUserException.class);
        thrown.expectMessage(format("User with id %s doesn't exist!", userId));
        final User existingUser = spy(new User());
        given(userRepository.findOne(eq("123"))).willReturn(existingUser);
        given(userRepository.save(eq(existingUser))).willReturn(existingUser);
     
        userService.deleteUser("123");
     
        verify(existingUser).setState(eq(User.State.DELETED));
    }
    {{</highlight>}}
    
Whereas the second approach gives more control over expressing where the test code should throw 
an exception, there's even better approach to design your `JUnit` tests, using powerful 
[AssertJ](https://joel-costigliola.github.io/assertj/) testing library.

## AssertJ approach

The one I found quite elegant and easy to use and understand comes from powerful `AssertJ` 
library, using static `Assertions.assertThatThrownBy` static method, as shown in the following
 test:

{{<highlight java>}}
@Test
public void failsDeletingUser() {
    final String userId = "123";
    given(userRepository.findOne(eq(userId))).willReturn(null);
    assertThatThrownBy(() -> userService.deleteUser(userId))
            .isInstanceOf(UserService.NoSuchUserException.class)
            .hasMessage(format("User with id %s doesn't exist!", userId));
}
{{</highlight>}}

It receives instance of `ThrowingCallable` functional interface (Java 8) which is quite handy 
since you can pass lambda implementation (as shown). Given interface has only one method, 
which we implemented as lambda above. The interface looks like:

{{<highlight kotlin>}}
public interface ThrowingCallable {
    void call() throws Throwable;
}
{{</highlight>}}

In upcoming version 5 of `JUnit` framework there’s no more option to use expected attribute in 
`@Test` annotation. Having two options there, my favourite will be option coming from `AssertJ` 
library.

Let me know your thoughts!

Until next time!