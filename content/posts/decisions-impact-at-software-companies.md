---
title: Impact of decisions at software companies
date: 2019-09-26T22:54:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
 - micro-services
 - enterprize
 - featured
---

The focus of this article is to cover some pros and cons of various decisions one software company can take. Software companies 
need to make decisions in order to move forward, and as we are about to see - they rarely bring only benefits to the company.

Let's analyse the impact of various decisions software companies tend to make. Listed are some decisions, that never came with only 
positive effects, and, quite frequently, cons are outweighing all the pros. 

Ready? Let's get started!

## Unification of software artifacts

{{< figure src="/posts/img/generate-project.png" position="center" style="border-radius: 8px;" caption="Waiter - same as before!" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

Many companies nowadays, when designing the software, move towards so called `micro-services`. For those that are not familiar with it - 
`micro-services` allow that software that company produces is composed of many small software pieces that are wired up to work together.

There are known pros and cons having entire company software developed as a single software artifact. If decision is taken towards employing 
`micro-services`, the top technology executives in many companies try to optimize structure of these small software components so that these 
little pieces need to be as similar as possible to each other.

What does that mean in practice? One of advantages of going `micro-services` way is that these small applications can be developed independently 
in terms of programming language being used.

Companies often try to enforce programming language being used, so that it allows for better developer utilization. In theory - if we have two 
teams, each developing own `micro-service`, we can more easily "borrow" available developer from the other team, if he already knows the 
programming language our team uses.

## Internal (shared) libraries

{{< figure src="/posts/img/shared-libs.png" position="center" style="border-radius: 8px;" caption="Versioning hell" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

It’s not so uncommon that some companies, with the best intent, tend to produce some kind of **company libraries**, which should, in theory, 
help eliminating redundant work that would have need to be done by many teams otherwise.

So, we come across libraries that are wrapping interactions with external systems, such as databases, messaging systems etc. They provide 
facilities that could be some of the following:

* Providing solutions for some legal requirements that need to be fulfilled when talking to third party systems. They often do some kind of 
encryption / decryption mechanisms, etc.
* Sometimes business is unsure whether some technology should be used, and wants to build an abstraction layer in front, so that if there’s 
a need to change to some other technology, the impact would be (in theory) smaller, since abstraction in front is guarding against changes. 
The issue with this approach is that it is very hard to build abstractions and at the same time keep all the features that underlying 
technology provides. 
* Sometimes there are team members that are still juniors, and business believes that underlying technology is out of their reach, so that 
providing abstraction layer in front should simplify interactions with such a systems.

The issue with these, internal libraries, is that they fail delivering what they claim to offer. Underlying technologies features leak out 
of these abstractions.  

These libraries often reduce features of the things they abstract away. Most often, the things abstracted away offer specific features, which 
are very useful, but if they would be exposed - it would be hard to replace them later with the technology that doesn't offer that. 

The developers that developed them move to new projects and can’t even maintain them anymore.

As soon as they are started being used, maintainers of these abstractions are not comfortable making changes knowing that there are quite some 
clients using them.

Clients of such libraries come with requirements for particular features which are not supported, and these libraries are soon getting tons of 
configuration options to support some features. 

Long story short here - there are numerous articles nowadays emphasizing issues with internally created libraries. Technologies are to be used 
directly. Shared libraries are considered an anti pattern nowadays in the world of `microservices`.  

The best strategy to mitigate such a inventions is to form well structured teams that understand well the constraints they need to respect, 
but are skilled to overcome them. Also, when having multiple teams that need to fulfill the same requirements may lead to sharing code 
snippets that other teams can just use, without dealing with shared libraries, which release is the one they need, etc. 

Long story short - internal inventions are rarely a good thing.

## "The Architects"

{{< figure src="/posts/img/meeting.png" position="center" style="border-radius: 8px;" caption="Let's decide upfront!" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

Mid or big size companies some times have special team, which is formed out  of senior engineers (sometimes even ex engineers included, often 
without clear criteria who should be the member) which are there to regulate technology rules within the company. Decisions scope can vary 
significantly, but it’s not uncommon that they provide the list of supported databases, programming languages to be used, frameworks to be 
chosen from, etc.

This, on the first go, sounds totally sane to let senior people regulate rules. 

But, there are many issues with such approach. Very often - such a groups upfront make decisions without enough context for making such a decisions. 
It can happen that decisions such as which storages are supported can be brought even before the applications landscape has been designed which 
would give way better context for making wise choices. 

It is not uncommon that there are whitelisted technologies to be used, and for extending such a lists special kind of meetings are organised so 
that when particular team needs a new tool to use, discussions often take place around why such a tool needs to be used. 

For larger companies, this is a significant impediment, since modern software developments requires efficient decision making. 

The key to overcome such a regulatory bodies is to form such a culture of independent and responsible teams, which are capable and mature enough to take 
decisions which are in the best interests of their company. 

One important aspect of organising technology department would be a complete focus on equally distributing people that are the most experienced. 
For such s roles there needs to be s compromise made, since often the best team leads are actually not the ones that are technically superior, 
rather ones that have a good balance of technology and social skills. 

Such a person usually encourages others to take more and more responsibilities, which basically produces new team leads in the future. There’s 
a time needed for such a process, but this the only way to scale technology organisation to meet company demands.

## Promoting people

{{< figure src="/posts/img/promotions.png" position="center" style="border-radius: 8px;" caption="And now - something completely different!" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

Inevitably, many companies that start growing, are faced with the fact that they need to position right amount of people 
on optimal number of positions. So, now we have a situation that small teams of couple of engineers start growing: new people 
are being hired to keep up with aggressive amount of business features needed. 

What happens often in such a cases is that companies do not pay enough attention to the fact that **people that are best skilled 
for the roles should be put in place**.

Such a organizations tend to make mistake to promote excellent engineers to become non technical, like product owners, etc. The biggest 
issue they tend to make is that they do not feel necessity to invest into transitioning these people into new roles.

These transitions are all but trivial: new roles require a lot more social skills, which can’t be built overnight.

Often, equally problematic, these ex engineers do not ask for help. They believe they would just make it somehow and that soon they will 
feel in the new role equally comfortable as they used to feel not so long time ago.

The point here would be: companies need to be prepared to the fact that scaling may happen and that as business grows - internal organizational 
structure need to be planned ahead. This is not a trivial task, but just promoting people without investing into their ability to deal with 
the kinds of things they never dealt before will have high chances of having them fail eventually.

## Obligatory time tracking

{{< figure src="/posts/img/time-tracking.png" position="center" style="border-radius: 8px;" caption="Time first - software last!" captionPosition="center" captionStyle="color: #4b4c4d;" >}}

In order to get an overview of it's employees activities, many companies make obligatory time tracking. Speaking from own experience, most productive 
companies I worked for didn't force us to fill in our time sheets.

My honest opinion is that the only way for company to be successful is that each activity that an employee does is either **employee centric** 
or **customer centric**

I can't find any correlation between time tracking and either employee or customer satisfaction. Speaking from the perspective of an employee, 
I can see the following negative impact of time tracking:

* It requires discipline, and discipline often fails.
* The time spent on time tracking or even thinking about it (in background) is the time that could have been used towards maximising value for 
the customer.

Until next time!  :wave:
