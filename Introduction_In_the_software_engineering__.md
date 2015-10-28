# Introduction

In the software engineering and development, there are many problems that are usually already solved or there exists a way of diminishing these problems using known patterns and best practices. Many such difficulties come already in the design phase of the software and developers better have the knowledge and right tools to design the software right. The trouble is that there is no "right" in software development. Many of the problems are so new, that nobody ever made an attempt of solving them. And of course, even experienced software developers cannot possibly know every problem the software can run into later in development. We can measure "right" only by success of the project, i.e. building the software according to customers needs, in time and on budget.

This however does not say anything about the project internal code and design quality. A successful project may be in its internals very brittle to change and the code may be so complex that maintenance becomes very hard. This can result in introduction of bugs into the code, which are consequently difficult to find and fix. 

So, one of the best things software engineers should have is knowledge of the patterns and best practices that were already used and proven working by someone else to tackle some of these common problems. Making the code and design easy to maintain, change, and extend. And that not only after the project is done but also during the development of the software itself, where numerous problems can emerge (e.g. customers requirements changed, or unexpected issue was found).

Thus choices taken prior starting the software development, even though they were thought through carefully, may not cover the problems that come during the development. This is were design patterns and best practices are the way of helping the software development out of the problems or easing them.

## Event Sourcing

It is not uncommon in software development world that principles make their way into every day usage long time after they were published or examined by their creators. Take an example of functional programming, principles of which were described in 1930s, implemented into Lisp programming language in 1950s but never being part of mainstream software development until the start of 21<sup>st</sup> century where a boom of new functional programming languages happened.

The Event Sourcing design pattern took a similar path. The core idea of Event Sourcing is being used for some time but not so much in typical software development. You can get the idea of this pattern from bookkeeping or bank accounts, where everything is stored as a list of accounting entries, or entries of money deposit and withdrawal. When you get your bank statement, you can see it is presented as a list of entries specifying what happened to your account each time the transaction happened and how much money was involved in the transaction. If you take all these entries and sum them (positive values for money deposit, and negative for withdrawal) you get your account balance. This is how banks worked and are still working - they keep all these entries as a history of account transactions and by summing them we get the account balance.
