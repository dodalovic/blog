---
title: Shared libraries in distributed systems
date: 2019-10-28T11:54:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
 - micro-services
 - enterprize
 - libraries
 - featured
---

I wanted to share some some experience that emerged while developing distributed systems in recent couple of years. More specifically - I’d like to 
share my experience related to **microservice** based architecture and use of shared libraries in such a systems. 

Let’s get started.

## About shared libraries

Shared libraries could be, in simple words, defined as **a piece of code, often developed by somebody else, and that we can use in a relatively 
straightforward way**. Often the ones providing shared libraries have a deep knowledge of that domain, so - it makes sense to use their solutions. 

I would like to make distinction between two kinds of libraries:

* Publicly available libraries
* In house libraries

## Publicly available libraries

{{< figure src="/posts/img/berlin-potsdam-2019-1.JPG" position="center" style="border-radius: 8px;" caption="Potsdam / Germany / 2019" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

The open source community nowadays provides huge number of useful libraries that are generic in their nature, and address common issues, that usually 
appear while developing software nowadays. 

In the *Java* ecosystem, we can find a huge set of various *Apache libraries* and so on. They are really well tested, have clear scope and there’s 
insignificant risk in using them.  

These libraries use  [semantic versioning](https://semver.org/) , are usually well documented, have predictable release process, and are very easy to 
integrate. There’s hardly any chance that developers can write themselves any better, or even if they could - there will always be more important 
things to focus on. 

They are distributed publicly, in different forms of channels, such as public **Maven** repositories for the **JVM** ecosystem, **npm** repositories 
for **JavaScript**, etc. 

Usually, all that we need to do is to just declare particular version of the dependency we want, and our build tools make sure it’s downloaded, and 
we’re good to go. 

### In house libraries

The main focus of this writing is actually the other kind of libraries that are present as well, especially in mid size or big size companies. 

What could be the domain of such a libraries? 

#### Evaluating technology to be used

{{< figure src="/posts/img/berlin-potsdam-2019-2.png" position="center" style="border-radius: 8px;" caption="Potsdam / Germany / 2019" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

Companies can face the situation that new technology has to be used, let’s say messaging system. There’s uncertainty about which one to be used, 
in case couple of similar solutions can be found on the market. In case of companies that have decent number of development teams, it may sound 
risky to directly start using some solution, and in case of being unhappy with the outcome - it would not be that easy to replace it with another 
without affecting all the teams using it. 

#### Fulfilling legal requirements when using some technology 

There are some cases that some legal requirements or special contracts with some clients force generation of some kind of shared libraries, that 
seem to address these requirements the best way there is. 

For instance - some clients may ask that all messages sent to messaging systems have to be encrypted using key for that particular customer. 

It feels very tempting, since there may be many teams using messaging system, to not repeat the same encryption / decryption logic for each and every 
team using it, but rather encapsulate that into separate module which everyone can just use. 

The challenges with this approach is how to implement these business requirements while keeping all the functionalities of the underlying technology at 
the same time. This layer requires full testing on its own. 

What also happens is that companies can dedicate group of engineers to create such a abstraction layer, but what happens after is that, often, this 
group of people is spread around different projects and disconnects pretty much from that shared library one.

Often responsibility over the library vanishes since these engineers have other software to build and maintain, and ownership over these shared libraries 
becomes less and less obvious. 

There are many resources pointing out that having these kind of shared libraries is something that companies should avoid doing at all costs. 
If possible, the requirements coming from specific clients should be discussed in depth, since it is not uncommon to have just some clients influence software 
significantly, while others can do perfectly fine without such a requirements. 

Nevertheless, in cases these kind of requirements can’t be negotiated at all, it may make sense to have the code that addresses them documented really 
well on an example of one service that is actually using it. That way other teams can just **copy paste the solution, understand it well, and be able 
to maintain it in their project.** Any time they discover bug in the existing solution, they can share the solution by sharing diff with the others, 
and original documentation can be updated as well. 

## Do repeat yourself

{{< figure src="/posts/img/berlin-potsdam-2019-3.jpg" position="center" style="border-radius: 8px;" caption="Potsdam / Germany / 2019" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

It turns out that a strategy to encapsulate all these business requirements may not be the strategy that’s wise to do. Libraries become bottleneck 
**due to fuzzy  ownership**, it’s release cycles, and very often lack of decent documentation. 

In my humble opinion copy-pasting solutions between multiple projects works quite well. It’s easy to integrate changes into own project and test it. 
Often people that haven’t built the original versions can spot bugs, create patches and notify the rest of the teams with the diffs that they may 
also like to use. That way moving forward is faster then dealing with the contribution to the shared libraries (which often involves discussions about 
who should be able to contribute to the library project, etc).

## Shared libraries are not portable everywhere

Additional reason for not creating shared libraries is that these are not so portable, at the end of the day. It may sound like that, in the *JVM* 
world, having jars is completely portable solution, but that’s very likely not the case since library may have been compiled with newer version of 
*Java* that the specific service that uses the library may use, or library itself may have transitive dependencies that may cause conflicts with 
versions already used in services, etc. 

Also, investing into them is not portable to the other languages that may exist in the system.

Knowing these limitations, it may seem clever to just document in detail the specification of what library brings to the client applications, with 
the code samples from the existing apps. That way the authors of the same functionality for the other programming language just need to read the 
specification, and implement the solution and document it the same way for all the existing languages. 

## Conclusion
In the world of distributed software, and having multiple development teams, the strategy to avoid shared libraries may sound as a sane strategy to 
deal with various business requirements that affect of code bases of multiple teams. 

Finding the right group of engineers to implement the initial solution and document it well is the most important step. After that, each development 
team needs to be able to understand the scope of the functionality  and to be able to integrate the solution into own services and eventually - 
**to be able to contribute to any enhancements needed**.

Until next time👋 