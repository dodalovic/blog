---
title: Primary school of IntelliJ IDEA
date: 2020-02-17T08:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - featured
  - software development
  - productivity
---

{{< figure src="https://upload.wikimedia.org/wikipedia/commons/1/1a/JetBrains_Logo_2016.svg" position="center" style="border-radius: 8px;" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

IntelliJ IDEA (**IDEA** shorter) is a cross-platform **IDE**, developed by [**JetBrains**](https://www.jetbrains.com/) company. They have **IDEs** for other programming languages as well, 
which are not covered by this post. All their tooling is built on a common core, which is a very robust and intelligent development platform. 

## Versions

**IDEA** comes in two flavors: **Community** and Ultimate [edition](https://www.jetbrains.com/idea/download/). **Community** comes free of charge. **Ultimate** edition is a commercial product, 
but knowing how much you can get out of it - I find the price very low.

## Where to get it from

A suggested way of downloading any **JetBrains** product is to download their [Toolbox app](https://www.jetbrains.com/toolbox-app/). Once you have it installed, you may choose any of their 
products. For this case, I suggest you download Intellij IDEA Ultimate. The **Ultimate** version comes with a one month trial period, which you can use to get the impression.

## Why do I need a commercial tool

Well, in case of `JVM` languages we could argue that there's only one serious contender in this space, **Eclipse**.  From my own experience, after using **Eclipse** for about 8 years, and having 
switched to **IDEA**, I could never go back to it. **IDEA** simply offers a very intelligent environment, with so many small or big features, that **Eclipse**  can’t compete with. 

There are also ongoing efforts to make **Visual Studio Code** contender in the JVM ecosystem, but having tried it recently, I can only say that there is a long way ahead of VSCode to make it a 
reasonable choice for developing JVM language based applications.

## What you get for the money?

* Fantastic **Git** client, absolutely better, in my humble opinion, than any other, especially knowing that it’s integrated into the IDE, thus reducing distraction when switching to a separate 
tool to manage source code. 
* Support for development in any popular framework on the market (I could argue that **IDEA** has better integration than any other **IDE** on the market)
* Whereas **Eclipse** enforces the concept of workspaces, **IDEA** doesn't. This allows for high flexibility how you keep your projects organized on your file system
* Amazing refactoring support, which can make a real difference when compared to the **Eclipse**
* JetBrains support, which was very friendly in case of any bug reported, so you get support for the money.

So, in case you're curious, join me on a ride towards learning the base concepts of **IntelliJ IDEA**.

## Projects

**IntelliJ IDEA** is organized around the concept of Projects. What is a project? A project allows us to group several so-called *modules* into one logical group. 

Examples of projects:

* *Single module project* - for instance, we have one git project that we want to work on
* *Multi-module project* - where each module can be one `git` project on its own - this is useful when you want to work on several projects simultaneously since modules are somewhat 
logically connected.
* *Empty project* -  we decide to start from scratch - we create an empty directory on the file system, and start adding modules to it. Modules we’re adding don’t have to be physically 
stored as subdirectories inside that project’s directory.

**IDEA** doesn’t impose any directory structure for your projects, which makes it very flexible in terms of allowing you to organize your physical directories to your liking. 

## Tool windows

After you create a project, you will be presented with various, so-called **tool windows**. Tool windows are, essentially, different aspects of your project. 
Examples of tool windows would be:

* **Project** - shows your modules with their source code
  {{< figure src="/posts/img/intellij-idea-project-tool-window.png" position="center" style="border-radius: 8px;" caption="IntelliJ IDEA project tool window" captionPosition="center" captionStyle="color: #4b4c4d;" >}}
* **Version control** - shows an overview of any changed code, where you can stage, commit, push your changes
  {{< figure src="/posts/img/intellij-idea-version-control-tool-window.png" position="center" style="border-radius: 8px;" caption="IntelliJ IDEA version control tool window" captionPosition="center" captionStyle="color: #4b4c4d;" >}}
* **Gradle / Maven** - shows your project modules, with the ability to run various build system actions
* **Terminal** - opens a terminal, supporting multiple sessions

## Editor area

Of course, like any other IDE/editor, the main purpose is to view/edit your source code, and that occupies the central area of the IntelliJ project. 
It can contain multiple tabs so that you can jump between different files, and you can also hide tabs, which advanced users prefer.
Similar to some other tools, IntelliJ also supports splitting editors horizontally and vertically.
Editor area shows expected things like code itself, line numbers, but also has visual indicators showing warnings/errors, and quick actions that can help you mitigate them.

## Run / Debug configurations

IntelliJ allows for running different execution targets inside the IDE itself

* Tests
* Building ant / Gradle / Maven projects
* Running applications (with main methods)

This allows for keeping your focus on tasks so that you don’t have to jump out from your IDE to your terminal back and forth.

## Project structure

Accessible via `⌘ + ;` shortcut as well as via File menu, we can fine-tune settings related to our currently opened project, like JDK version, language level, etc

{{< figure src="/posts/img/intellij-idea-project-structure.png" position="center" style="border-radius: 8px;" caption="IntelliJ IDEA project structure" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

## IntelliJ preferences

Accessible via `⌘ + ,` shortcut as well as via File menu, we can set the overall settings of our IntelliJ installation, compiler, build tools, appearance, memory settings, etc

{{< figure src="/posts/img/intellij-idea-preferences.png" position="center" style="border-radius: 8px;" caption="IntelliJ IDEA preferences" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

## Plugins

IntelliJ is extensible, using plugins, which you can install from the Marketplace, or even build and publish on your own. Themes are considered plugins, so you can find the ones you 
like there.

{{< figure src="/posts/img/intellij-idea-plugins.png" position="center" style="border-radius: 8px;" caption="IntelliJ IDEA plugins" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

In the following posts, I will provide a series of screencasts to help you get started faster with **IDEA**!

That was all for today! Hope you liked it!

That’s all folks, hope that helped!
