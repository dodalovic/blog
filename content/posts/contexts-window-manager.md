---
title: Contexts MacOS window manager
date: 2020-02-07T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - featured
  - productivity
---

I've recently come across [Contexts](https://contexts.co/) window manager and thought it would be interesting sharing why I found it to work \
particularly well in the context of my productivity.

Contexts come with a trial period, upon which I decided to purchase a license. Why would one even care to switch to some
alternate window manager? Let's dive deep into it!

## Why do I care for window switching efficiency at all?

First of all - there's nothing wrong with MacOS built-in window switcher. It does what people expect it to, the way window switching works
pretty much on other platforms as well. 

In my daily work, I've got around 5 - 10 apps running in parallel. I frequently switch between them. Since I do care about my productivity,
I searched for the tool that would give me a chance to rapidly fast switch between running apps. 

Each time I need to switch between running
applications, my focus gets affected. I do not have an estimate of how often I switch between the running apps daily, but I'm pretty sure
it's a massive number. A simple reduction of the duration of a context switch between the apps helps me minimize time wasted throughout the
day.

The other benefit I noticed is that instead of `⌘ + ⇥` being pressed even if I maybe do not have a compelling reason to do it, using
Contexts helps me focus on what I want to do upfront. This especially applies when using Search using a function key in which case you precisely
need to know where you want to jump to.

## Installation

The installation process is fairly straightforward, it consists of downloading a macOS package installer from their website and executing a
standard installation process upon downloading the package. 

## Window switching modes

There are a couple of window switching modes, which I’ll shortly walk you through

## Standard MacOS `⌘ + ⇥` replacement
 
This is a sample showing how alternative window switching looks like in action. By invoking standard `⌘ + ⇥` shortcut, I get something like:

{{< figure src="/posts/img/contexts-1.gif" position="center" style="border-radius: 8px;" caption="Window switching" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

Now, while holding `⌘` pressed, I can just cycle through existing apps, by pressing `⇥` which is the same as in standard MacOS switcher. 
What differs now is that you can additionally press `j` or `n` to go down, or `k` or `p` to go up. This is especially handy if you already have
these keys and their actions in your fingers (vim users). While holding the `SHIFT (⇧)` button we can reverse the cycling direction.

## Gestures

An alternative way to switch between the apps is to use two fingers and slide them down from either the left top or right top side of the
trackpad. Now you can entirely avoid any typing, you can navigate through the apps by just moving your fingers up / down until you find the
app you need! Handy for those who are more gesture oriented!

## Search modes

Contexts offer a couple of alternatives for how you can search through running apps. I prefer using it, so let’s cover those as well!

### Standard search mode

I configured my Contexts to use `⎇ + ⇥` shortcut to enter search mode. By pressing this combination you can start typing to narrow down
between running apps. This is quite handy if you don't have an idea which apps are currently running and you want to be
able to immediately search through them.

{{< figure src="/posts/img/contexts-2.gif" position="center" style="border-radius: 8px;" caption="Standard search mode" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

### Fuzzy search

With Contexts, you can even do a fuzzy search, so you can type for `chm` (instead of expected `chr`) and Chrome would be fuzzy matched. Search is
case insensitive, so typing either `chr` or `Chr` would work.

{{< figure src="/posts/img/contexts-4.gif" position="center" style="border-radius: 8px;" caption="Fuzzy search" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

### Search using the `fn` key

This is my preferred mode to rapidly search for some application. This is how it works: 

1. Let’s assume one of currently running applications is Google Chrome
2. Press and hold function key (`fn`) 
3. Start typing letters of the application you're searching, let's say goo if you want to switch to Google Chrome

My experience is that this mode can push productivity to the extreme, once it becomes part of your muscle memory!

### Search between windows of the same application

I’ve personally had cases that standard MacOS switcher didn’t show all the individual windows of the same application. In my case, we’re
talking about Intellij IDEA IDE. Having multiple windows of IntelliJ is my standard setup and I was annoyed with that behavior.
Furthermore - I wasn’t able to find any workaround.

This is where Contexts came to the rescue. By pressing ``⌘ + ` `` while an application with multiple windows is active, a small popup window
will appear showing all the windows of a given app. If we continue holding the `⌘` key and keep pressing backtick it will keep cycling through
available application windows.

{{< figure src="/posts/img/contexts-3.gif" position="center" style="border-radius: 8px;" caption="Cycling through the windows of the same app"
captionPosition="center" captionStyle="color: #4b4c4d;" >}}

## Other features

On top of already mentioned, Contexts supports a couple of additional features. You can:

* Theme your window list (Dark mode supported 🙂)
  {{< figure src="/posts/img/contexts-5.png" position="center" style="border-radius: 8px;" caption="Dark mode" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

* Define dimensions of the window list, with configurable font size as well
* You can have a sidebar with applications, that you can activate by moving the trackpad pointe to one of the corners of your screen (depends on how
you configure it)

Hope this post makes Contexts something you would like to try on your own. 

That’s all folks, hope that helped!