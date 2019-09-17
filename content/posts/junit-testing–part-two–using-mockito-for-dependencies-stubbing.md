---
title: JUnit testing - part II – using mockito for dependencies stubbing
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - testing
  - software patterns
  - featured
---

So, in case you haven’t read [the first part](/posts/2017/07/junit-testing-part-i-setup-with-simple-example/) in this series, 
which is giving some basic introduction to the idea of `JUnit` testing – go ahead, I’ll wait till you’re back so that we can 
proceed with the next steps.

:woman: **Yes, I’ve got the basics, go on.**

Great. Let’s now proceed with getting testing support in case our *System Under Test (SUT)* has some collaborator objects, 
which is the case most often, and we want to configure behavior of these objects in our `SUT` tests. For such a thing we’ll 
use `Mockito` library. In real life our `SUT` depends on other collaborators to help him complete his responsibility. Might be 
that we’re about to build an app that is some sort of music streaming one, and we’re creating UserService to list favorite 
playlists for current user. It may be that we’ll make `UserService` dependant on `PlaylistService` that is capable of 
returning playlists for particular user. So, in such a case, in order to test our `UserService` in isolation, we’ll have to 
configure our collaborator – `PlaylistService` in terms of behavior, so that we can test our `UserService` for such a 
behavior(s). During application runtime, in various situations, our collaborators might return some values, sometimes they 
return empty collections, or might throw an exception. Idea is that we’d like to mimic these kind of situations in our tests, 
so that we confirm our SUT works as planned for different collaborators behaviors.

:woman: **OK.. How do I get `Mockito`? Hope I don’t need bunch of Jars downloaded and configured – just to get started.**

No, not at all. We’ll continue coding from where we left off after completing the first part. We’ll use `Maven` to help us do 
dependency setup, so we’ll open our pom.xml and insert our dependency:

{{<highlight xml>}}
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>1.10.19</version>
    <scope>test</scope>
</dependency>
{{</highlight>}}

All we had to do is inserting this snippet inside `<dependencies>` tag of our `pom.xml`. `Maven` will get `mockito-core-1.10.19.jar` 
downloaded for us and ready to use in our tests.

:woman: **Can I see `Mockito` in action? You can explain me the details on the fly…**

Sure. Once we did dependency setup, we can create simple test case to show the first basic steps. Let’s create `MockListTest.java`

{{<highlight java>}}
package com.mydomain.mock_list;
 
import org.JUnit.Before;
import org.JUnit.Test;
import org.mockito.BDDMockito;
import org.mockito.Mock;
import org.mockito.MockingDetails;
import org.mockito.MockitoAnnotations;
 
import java.util.List;
 
import static org.JUnit.Assert.assertTrue;
import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.mock;
 
public class MockListTest {
 
    @Mock
    List<Integer> integerList;
 
    @Before
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);
    }
 
    @Test
    public void mockListWithoutAnnotations() throws Exception {
        /* given */
        final List mockedList = mock(List.class);
 
        given(mockedList.get(3)).willReturn(3);
        given(mockedList.size()).willReturn(1);
 
        mockedList.add(2);
        mockedList.add(3);
        mockedList.add(4);
        mockedList.add(5);
 
        /* when */
        final int listSize = mockedList.size();
 
        /* then */
 
        final MockingDetails mockingDetails = BDDMockito.mockingDetails(mockedList);
        assertTrue(mockingDetails.getInvocations().size() == 5);
        assertTrue(listSize == 1);
    }
 
    @Test
    public void useInjectedMock() throws Exception {
        given(integerList.size()).willReturn(1, 2, 3);
        integerList.add(3);
        assertTrue(integerList.size() == 1);
        integerList.add(4);
        assertTrue(integerList.size() == 2);
        integerList.add(5);
        assertTrue(integerList.size() == 3);
    }
}
{{</highlight>}}

Let’s first analyze `mockListWithoutAnnotations()` test method. We statically imported several `Mockito` methods in imports area, 
such as `Mockito.mock`, `BDDMockito.given`, so that our code is more readable. The first usage of `Mockito` API is a call to `mock()` 
method, which creates mocked instance of given interface / class.

:woman: **Mocked instance?**

Yes. In real life we mock our collaborator objects. Here we just mocked an instance of `List` interface. We use mocking to setup 
some behavior of collaborators, and we want to check how our `SUT` behaves in such a case.

Tests given above are not realistic since they don’t test any `SUT`, here we’re just creating demo how to use APIs. `mock` method 
receives `Class` parameter, which is the type we wan’t to mock.

Another way is to use `@Mock` `Mockito` annotation. We can just have a class field annotated with annotation and some additional plumbing: 

* we should call `MockitoAnnotations.initMocks(this)` inside `@Before` annotated setup method (it’s called by framework once before every test method is executed).
* annotate Test class with `@RunWith(MockitoJUnitRunner.class)`

:woman: **What happens if we don’t configure mocked object and it’s methods get called?**

Non `void` methods return by default an “empty” value appropriate for its type (`null`, `0`, `false`, empty collection).

:woman: **You said we can configure behavior of collaborator objects… How?**

There’s a sequence called **Arrange – Act – Assert (AAA)** which is to be followed in `JUnit` tests implementation. In **Arrange** 
phase we create mock instances, and configure their behavior. After that, in **Act** phase, we call `SUT` method we want tested. Finally, 
in **Assert** phase, we assert various conditions to check if `SUT` executed as expected in given context.

There’s another way to express your tests, which is part of **Behavior Driven Development**, which states test steps as **Given – When – Then (GWT)**. 

`BDDMockito.given(mockedList.get(3)).willReturn(3)` is an example of stubbing mocks for expected behavior using `GWT`. Pretty self explanatory – we 
configure mock to return value 3 when mocked List instance is asked to return value at the index of 3.

`BDDMockito.given` returns as a result instance of `BDDMyOngoingStubbing`, which has the following API to use when stubbing mocks in our tests: `willAnswer`, 
`will`, `willReturn`, `willThrow`, `willCallRealMethod`. Most often you’ll probably use `willReturn` and `willThrow` when stubbing your collaborators.

Using `AAA` style, we can configure the same stubbing as `GWT` using `Mockito.when(mockedList.get(2)).thenReturn(2)` syntax. We will not cover the differences 
between `AAA` and `GWT` styles. I use `GWT` style but you can use the one you feel comfortable with.

:woman: **What about argument matching?**

`Mockito`, by default, uses equals() for arguments matching. Let’s see it in action: 

{{<highlight java>}}
@Test
public void testArguments() throws Exception {
    final String someString = "some string";
    final String notMe = "not me";
    final List<String> mockedList = (List<String>) mock(List.class);
 
    given(mockedList.add(someString)).willReturn(true);
    given(mockedList.add(Matchers.startsWith("can't add"))).willReturn(false);
    given(mockedList.add(Matchers.eq(notMe))).willReturn(false);
 
    assertTrue(mockedList.add(someString));
    assertFalse(mockedList.add("can't add 1"));
    assertFalse(mockedList.add("can't add 2"));
}
{{</highlight>}}

`Matchers` and `AdditionalMatchers` APIs provide large set of useful matchers we can use. We have fine grained control of configuring 
mocks using these. Given above, we’re using `Matchers.startsWith` and `Matchers.eq`. So, we can configure mocks to return specific value 
for exact value of argument it receives, or some other value if argument is / isn’t null, or in case of `String` we can configure behavior 
depending on if argument starts with some sequence, ends with, and so forth. Quite often you may find useful family of `any()` methods, 
so you can use it to mock methods of particular type, so if mock method receives `int` you can mock it using `Matchers.anyInt()` matcher.

:woman: **What about stubbing `void` methods?**

Let’s do it with an example:

{{<highlight java>}}
@Test
public void stubVoidMethods() throws Exception {
    List<String> list = ((List<String>) mock(List.class));
 
    BDDMockito.willDoNothing().given(list).clear();
    BDDMockito.willThrow(Exception.class).given(list).clear();
 
    Mockito.doNothing().when(list).clear();
    Mockito.doThrow(Exception.class).when(list).clear();
}
{{</highlight>}}

`List.clear()` is a `void` method, and we can configure `void` methods to either do nothing (which is default, so we don’t need to 
configure this) or to throw an exception, as shown in the snippet above.

:woman: **And how do I verify if mocked collaborators were actually called?**

For such a purpose we can call `Mockito.verify` API. Verification example:

{{<highlight java>}}
@Test
public void verificationExample() throws Exception {
    final Calculator calculator = mock(Calculator.class);
    verify(calculator, Mockito.never()).add(anyLong(), anyLong());
    calculator.divide(2, 5);
    verify(calculator, times(1)).divide(2, 5);
    verify(calculator, atMost(0)).multiply(anyLong(), anyLong());
    verify(calculator, never()).add(anyLong(), anyLong());
    Mockito.verifyZeroInteractions(calculator);
}
{{</highlight>}}

`verify` has a following signature: `Mockito.verify(Mock mock,VerificationMode mode)`. It receives `VerificationMode` as second parameter, 
and `Mockito` class has some built-in verification modes at your disposal: `times(int)`, `atMost(int)`, `never()` and so on. Given example 
above should be pretty self-explanatory. Feel free to explore all verification modes and how to use them by reading `Mockito` Javadoc. 
`Mockito` class also contains `verifyZeroInteractions` static method that receives varargs of mocks, and returns true if there were no 
interactions with given mocks, false otherwise.

:woman: **Can I capture actual value passed to mock, in order to make some asserts on it?**

Great question! Mockito has `ArgumentCaptor<T>` class that is to be used in such a case. Let’s show how to do that  with simple example. 
Say we have a class:

{{<highlight java>}}
package com.mydomain.sut_and_collaborator;
 
public class MySystemUnderTest {
    private Collaborator collaborator;
 
    public MySystemUnderTest(Collaborator collaborator) {
        this.collaborator = collaborator;
    }
 
    public boolean doSomeStuff(String withArgument) {
        return collaborator.doStuff(withArgument);
    }
}
{{</highlight>}}

Our Collaborator looks like:

{{<highlight java>}}
package com.mydomain.sut_and_collaborator;
 
public class Collaborator {
    public boolean doStuff(String withArgument) {
        return withArgument.contains("some-thing");
    }
}
Finally, we’re testing our system under test:

@Test
public void argumentCaptor() throws Exception {
    final Collaborator collaborator = mock(Collaborator.class);
    ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
    final boolean result = new MySystemUnderTest(collaborator).doSomeStuff("my param");
    verify(collaborator).doStuff(captor.capture());
    assertTrue(captor.getValue().equals("my param"));
    assertFalse(result);
}
{{</highlight>}}

We’re calling `doSomeStuff` on `MySystemUnderTest` class. All it does is delegating a call to `Collaborator` class. We want to assert that 
collaborator object received expected value as an argument. In order to capture value that collaborator received, we need to create 
`ArgumentCaptor<T>`, where `T` is type of argument method of collaborator object receives. In our case what gets executed is `collaborator.doStuff(String arg)`. 
So, we need argument captor of `String` type. So, in order to capture value passed to collaborator during `SUT` method test, we need to:

* Create `ArgumentCaptor<T>` instance
* Execute method call on `SUT` (that calls our collaborator method)
* verify collaborator method is executed passing `captor.capture()` as a method argument
* call `captor.getValue()` to obtain captured value

`ArgumentCaptor<T>` has also `getAllValues() : List<T>` method, that either returns all values if collaborator method receives varargs, or, 
in case method was called multiple times – list containing these values. Feel free to experiment with captor to get used to technique of 
capturing mock method arguments. Argument captor can be created at a test class field level by just putting `@Captor` annotation (note that 
the same configuration is required as for `@Mock` usage – described above) on the field itself, e.g:

{{<highlight java>}}
@Captor
ArgumentCaptor captor;
{{</highlight>}}

:woman: **Can I somehow stub real objects behavior, not mocked ones only?**

Yes, `Mockito` provides support for that. Usually, you don’t want to mock real objects, but in case you need that, `Mockito` has 
`Mockito.spy` API, as well as `@Spy` annotation. Basic idea is that we need to create a spy (proxy object) that will delegate all 
calls to real object, unless we say we want to override behavior of real object method(s). Example:

{{<highlight java>}}
@Test
public void spyExample() throws Exception {
    final MySystemUnderTest realSUT = new MySystemUnderTest(new Collaborator());
    final MySystemUnderTest spySUT = Mockito.spy(realSUT);
    final String argument = "Argument";
    doReturn(false).when(spySUT).doSomeStuff(argument);
    final boolean result = spySUT.doSomeStuff(argument);
    assertFalse(result);
}
{{</highlight>}}

An example of using `Spy` annotation (note that the same configuration is required as for `@Mock` usage – described above) is having 
a field of our Test case declared such as:

{{<highlight java>}}
@Spy
MySystemUnderTest systemUnderTest = new MySystemUnderTest(new Collaborator());
{{</highlight>}}

If we want to stub behavior of particular `SUT` spy, we need to use either of `doXXX` or `willXXX` methods family. In our case we called

{{<highlight java>}}
doReturn(false).when(spySUT).doSomeStuff(argument);
{{</highlight>}}

Using Behavior Driven style, we’d accomplish the same using:

{{<highlight java>}}
willReturn(false).given(spySUT).doSomeStuff(argument);
{{</highlight>}}

This way, in case we really need this feature, we can override method behavior of real object.

:woman: **Are there any limitations when using `Mockito`?**

Yes. Although there are hacks how to make workarounds (which might lead to tests hard to understand / maintain), Mockito can’t:

* mock final classes and enums
* mock final / static / private methods

## Key takeaways

* Mockito is a helper library tailored to perfectly fit `JUnit` tests development
* It provides us facilities to configure behavior of our `SUT`’s collaborator(s)
* With quite a lightweight syntax, it’s pretty easy to use & configure for own needs
* Basic idea is to mock collaborator objects for a specific behavior and after that test our SUT for given collaborator behavior.

Source code can be checked out from [Github](https://github.com/dodalovic/blog-JUnit-testing)

Stay tuned and please – don’t forget to subscribe in case you’re eager to find out what’s coming next in upcoming posts.