# Introduction

In the software engineering and development, there are many problems that are usually already solved or there exists a way of diminishing these problems using known patterns and best practices. Many such difficulties come already in the design phase of the software and developers better have the knowledge and the right tools to design the software right. The trouble is that there is no single "right", a silver bullet, which can be used every time in software development. Many of the problems are so new that nobody ever made an attempt of solving them. And of course, even experienced software developers cannot possibly know every problem the software can run into later in development. We can measure "right" only by success of the project, i.e. building the software according to customer's needs, in time, and on budget.

This however does not say anything about the project internal code and design quality. A successful project may be in its internals very brittle to change and the code may be so complex that maintenance becomes very hard. This can result in introduction of bugs into the code that are consequently difficult to find and fix. 

So, one of the best things software engineers should have is knowledge of the patterns and best practices that were already used and proven working by someone else to tackle some of these common problems while making the code and design easy to understand, maintain, change, and extend. Not only after the project is done but also during the development of the software itself, where numerous problems can emerge (e.g. customers change their requirements, or a never expected issue is found).

Choices taken prior starting the software development, even though they were thought through carefully, may not take into account problems that may come during the development. This is where design patterns and best practices are the way of helping the software developers out of the problems or easing them.

One of the design patterns that were not known to me until recently is Event Sourcing and its companion CQRS (Command Query Responsibility Segregation) pattern. By reading through articles and watching conference talks informing about the pattern I found it so interesting and powerful that I wanted to get deep into it and try it myself.

## Goals

This diploma thesis describes the basic principles of Event Sourcing pattern and the path taken in implementing it into an existing software project written in Java - Integration Portal - in which I practically examine the benefits of it over the original design. 

The first part of the thesis describes the idea of Event Sourcing and the Command Query Responsibility Segregation (CQRS) design pattern which is usually implemented together with Event Sourcing. The second part introduces the Integration Portal project and its original design that was, as part of this diploma thesis, subjected to refactoring into Event Sourcing and CQRS design. Finally, the whole process and the experience is described together with a discussion of the issues that were introduced by the refactoring with suggestions of how they can be handled.

In conclusion, this thesis should provide enough of necessary information to get the idea of Event Sourcing and CQRS and descibe the advantages and disadvantages of this design, in general and in Java implementation.