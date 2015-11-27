# Introduction

In software engineering and development, there are often many problems that are already solved, or there are known patterns and best practices that provide a way of diminishing them. Many such difficulties come early in the design phase of the software and developers had better have the knowledge and the proper tools to design the software in the right way. The trouble is that there is no single "right way", a silver bullet, which can be used universally in software development. Many of the problems are so new that nobody has made an attempt to solve them. And of course, even experienced software developers cannot possibly know every problem the software can run into later in development. We can measure "right" only by success of the project, i.e. building the software according to customers' needs, in time, and on budget.

This, however, does not say anything about the project internal code and design quality. A successful project may be very brittle to change in its internals and the code may be so complex that maintenance becomes very hard. This can result in the introduction of bugs into the code that are consequently difficult to find and fix. 

So, one of the best things software engineers can have is knowledge of the patterns and best practices that were already used and proven to work by someone else. This knowledge allows them to tackle some of these common problems while making the code and design easy to understand, maintain, change, and extend. This applies not only after the project is done but also during the development of the software itself, where numerous problems can emerge (e.g. customers change their requirements, or a never expected issue is found).

Choices taken prior to starting the development of some software, even if they were thought through carefully, may not take into account problems that might come during the development. This is where design patterns and best practices help the software developers to eliminate or ease the problems.

Two of the design patterns that were not known to me until recently are Event Sourcing and its companion CQRS (Command Query Responsibility Segregation) pattern. By reading through articles and watching conference talks detailing the pattern, I found the subject so interesting and powerful that I wanted to get deep into it and try it myself.

## Goals

This diploma thesis describes the basic principles of the Event Sourcing pattern and the path taken in implementing it into an existing software project written in Java - Integration Portal - in which I practically examine its benefits over the original design. 

The first part of the thesis describes the idea of Event Sourcing and the Command Query Responsibility Segregation (CQRS) design pattern which is usually implemented in tandem with Event Sourcing. The second part introduces the Integration Portal project and its original design that was, as part of this diploma thesis, subjected to refactoring into Event Sourcing and CQRS design. Finally, the whole process and the experience are described together with a discussion of the issues that were introduced by the refactoring and suggestions of how they could be handled.

In conclusion, this thesis should provide enough necessary information to obtain a basic understanding of Event Sourcing and CQRS and describe the advantages and disadvantages of this design, both in general and in Java implementation.