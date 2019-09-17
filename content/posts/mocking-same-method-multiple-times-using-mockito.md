---
title: Mocking same method multiple times using Mockito? Can't be easier than that!
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - testing
  - software development
  - java
---

This is fairly short blog post. I recently had the case I needed to mock the same method twice during my `Junit` test case, with different behavior.

To shorten your path how that is being made, take a quick look at :

```java
package com.mydomain;

import org.junit.Test;
import org.mockito.Mockito;

import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.*;

public class RepeatedMocker {
    @Test
    public void getsRightVersionWithNoRepeat() throws Exception {
        VersionManager versionManager = Mockito.mock(VersionManager.class);
        doReturn(1).when(versionManager).getVersion();
        SystemUnderTest systemUnderTest = new SystemUnderTest(versionManager);
        Optional<Integer> version = systemUnderTest.getVersion();
        assertThat(version.get()).isEqualTo(1);
        verify(versionManager).getVersion();
    }

    @Test
    public void getsRightVersionWithRepeat() throws Exception {
        VersionManager versionManager = Mockito.mock(VersionManager.class);
        doReturn(-1).doReturn(1).when(versionManager).getVersion();
        SystemUnderTest systemUnderTest = new SystemUnderTest(versionManager);
        Optional<Integer> version = systemUnderTest.getVersion();
        assertThat(version.get()).isEqualTo(1);
        verify(versionManager, times(2)).getVersion();
    }

    @Test
    public void failsGettingRightVersion() throws Exception {
        VersionManager versionManager = Mockito.mock(VersionManager.class);
        doReturn(-1).doReturn(-1).when(versionManager).getVersion();
        SystemUnderTest systemUnderTest = new SystemUnderTest(versionManager);
        Optional<Integer> version = systemUnderTest.getVersion();
        assertThat(version.isPresent()).isFalse();
        verify(versionManager, times(2)).getVersion();
    }

    private static class SystemUnderTest {
        private VersionManager versionManager;

        SystemUnderTest(VersionManager versionManager) {
            this.versionManager = versionManager;
        }

        Optional<Integer> getVersion() {
            int version = versionManager.getVersion();
            if (version == -1) {
                System.out.println("Trying once more, version can't be 1 ....");
                version = versionManager.getVersion();
                if (version == -1) {
                    System.out.println("Error: version can't be 1!");
                    return Optional.empty();
                }
            }
            return Optional.of(version);
        }
    }

    private static class VersionManager {
        int getVersion() {
            return Math.random() < 0.5 ? 1 : -1;
        }
    }
}
```

To shorten solution, I've implement class to be tested in the `Junit` test class itself. The class to test is `SystemUnderTest`. It has only one `public` method, `getVersion`, which basically just delegates 
that task to `VersionManager` dependent class. Here, we have some dummy implementation in `VersionManager`, where our method randomly returns `1` or `-1`. Our `SystemUnderTest` will retry retrieving correct 
version once more, if `VersionManager` returns `-1` in the first attempt. It will eventually, if it again receives `-1` from `VersionManager`, return an empty `Optional`.

Long story short - when we need to mock the same method differently more than once, we can just chain it as seen in above snippet.

```java
doReturn(-1).doReturn(1).when(versionManager).getVersion();
```

Given that, if our dependency class's method is called more than once during our system under test method call, it would use that different behavior you specify in this `doReturn()`....`doReturn()` methods chain.

Fairly simple!

You can find source code @  [Github](https://gist.github.com/dodalovic/3356c35925589bd2be843d71a931f51a)

Until next time!