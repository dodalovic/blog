---
title: Avoid NullPointerException - use Java 8 Optional
date: 2018-12-28T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - software development
  - java
---

This blog post should start with a popular quote:

> Tony Hoare introduced Null references in ALGOL W back in 1965 “simply because it was so easy to implement”, says Mr. Hoare. He talks about that 
>decision considering it “my billion-dollar mistake”.

Instead of dealing with `null` / `not-null` references, `Java 8 JDK` ships with new type: `Optional<T>`. How to use it?

Say we have a use-case where our client code calls some `API` which can return some value which is optional:

```java
User user = userService.getUser("username", "password");
System.out.println(user.getLastName());
```

Might be that there's no user with given credentials. In that case user reference might be `null`, but our client code forgot to check that one. 
`NullPointerException` will occur when trying to print out that user, when calling `getLastName()` method.

In such a cases,` Optional<T>` comes to the rescue. So, we could use it and rewrite upper snippet using it:

```java
Optional user = new UserService().getUser("username", "password");
if (user.isPresent()) {
    System.out.println(user.get().getLastName());
}
```

Important thing to notice is that whenever you see `API` is returning `Optional<T>` value, it should be very obvious that we should check value 
for presence before calling `.get()` because if the `Optional` instance is empty, calling `.get()` in such a case will result in `NoSuchElementException` 
being thrown.

We can see that we should design our `API` not to use null reference in case when data is missing, rather using `Optional` type in such a case. 
Given snippet above, we can see that `Optional` has `isPresent()` method which helps us checking if value is there or not. In case value is present, 
we can unwrap the value using `get()` call on `Optional`.

If we're designing `API` that can return some value optionally, we should do it the following way:

```java
class UserService {

    public Optional getUser(String username, String password) {
        // assume legacy userDao could return null for user
        User user = userDao.getUser(username, password);
        if (user != null) {
            return Optional.of(user);
        }
        return Optional.empty();
    }
}
```

In case return value from some, let's say legacy code, can be `null`, we should first check that fact, and in case of not `null` value, we should 
wrap value using `Optional.of`, or in other case we should return empty optional, using `Optional.empty()`.

There are several interesting methods present in the `Optional` `API`, and we'll demo their usage:

```java
public void ifPresent(Consumer consumer) {
public<u> Optional<u> map(Function mapper) {
public T orElse(T other) {
public T orElseGet(Supplier other)
public Optional filter(Predicate predicate) {
```

## Optional.ifPresent

> If a value is present, invoke the specified consumer with the value, otherwise do nothing.
> 
> Parameters:
> `consumer` - block to be executed if a value is present

Example:

```java
new UserService().getUser("username", "password")
        .ifPresent(System.out::println);
/* System.out.println is Consumer<User> and will receive
 non empty User instance as parameter */
```

## Optional.map

> If a value is present, apply the provided mapping function to it, and if the result is non-null, return an Optional describing the result. 
> Otherwise return an empty Optional.
>
> Parameters:
> `mapper` - a mapping function to apply to the value, if present

Example:

```java
final String username = Optional.of(new User("username", "password"))
                .map(User::getName)
                .orElse("Fallback username");
/* In case optional is not empty, mapping function will be applied,
  otherwise empty optional will be returned */
```

## Optional.orElse

> Return the value if present, otherwise return other.
>
> Parameters:
> `other` - the value to be returned if there is no value present, may be null

Example:

```java
final User user = new UserService().getUser("username", "password")
.orElse(new User("root", "generic"));

/* In case userService.getUser returns empty optional,
'root' user will be returned and assigned to user variable */
```

## Optional.orElseGet

> Return the value if present, otherwise invoke other and return the result of that invocation.
> 
> Parameters:
> `other` - a Supplier whose result is returned if no value is present

Example:

```java
new UserService().getUser("username", "password")
                .orElseGet(() -> FallbackUserService.getUser("username", "password"));

/* In case userService.getUser returns empty optional,
we'll try to get user calling FallbackUserService.getUser API */
```

## Optional.filter

> If a value is present, and the value matches the given predicate, return an Optional describing the value, otherwise return an empty Optional.
> 
> Parameters:
> `predicate` - a predicate to apply to the value, if present

Example:

```java
new UserService().getUser("username", "password")
                .filter(user -> user.getName().length() > 5)
                .ifPresent(System.out::println);
/* In case userService.getUser returns non empty optional,
we'll first make sure to check if user's name is longer than 5 characters */
```

## Conclusion

If we're writing `API` that can in some cases return meaningful value, and in other `null`, we should, instead of returning `null` or not `null` 
reference - return `Optional` with wrapped value or empty `Optional`. Same applies if we have some legacy code that could return `null`.

Hope you enjoyed reading and I suggest further exploring `Optional` `API`.