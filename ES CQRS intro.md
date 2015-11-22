# Event Sourcing and CQRS design patterns

In this chapter, the core ideas of Event Sourcing (ES) and Command Query Responsibility Segregation (CQRS) design patterns are described. The basic principles and terms of these patterns are presented to the reader so they acquire enough information to understand the next chapters.

## Introduction to Event Sourcing and CQRS

It is not uncommon in software development world that principles make their way into every day usage long time after they were published or examined by their creators. Take for example functional programming, principles of which were described in 1930s, implemented into Lisp programming language in 1950s but never being part of mainstream software development until the start of 21<sup>st</sup> century where a boom of new functional programming languages happened.

The Event Sourcing design pattern took a similar path. The core idea of Event Sourcing has been used for ages but not so much in typical software development. You can get the idea of this pattern from bookkeeping or bank accounts where everything is stored as a list of accounting entries, or entries of money deposit and withdrawal. When you get your bank statement, you can see it is presented as a list of entries specifying what happened to your account each time a transaction happened and how much money was involved in each transaction. If you take all these entries and sum them (positive values for money deposit, and negative for withdrawal) you get your total account balance. This is how banks worked and are still working - they keep all these entries as a history of account transactions and by summing them we get the account balance.

Now, let's get back to software development. A traditional software design that you can find in many books and online tutorials usually stores things by keeping only the current state of the application in a relational or document database. This may seem as something that is really needed from the system because the users of the application usually want to see the current data and interact with it. However, by keeping only the current state (balance of a bank account) we lose a lot of valuable historic data. We lose the information about the time a state change happened, who invoked the change, and so on. This historic data may remind you of something called an audit log. 

An audit log is the very same thing as the bank statement from the accounting example. It is effectively a log of events that happened to the account in its entire existence. Many systems actually need an audit log (banking systems by their nature do) for the purpose of regulation or auditing. Systems administrators may use the audit log to learn who did what in the system and track the changes. In many systems, an audit log is a separate module that is updated by the code handling the state changes. However, this way may be error-prone as developers who make changes to the code must be very disciplined and know to update the audit log to store the correct event into the log. As the system grows bigger, it may take a lot of effort to maintain such audit log and more importantly prove its correctness!

Event Sourcing takes the opposite approach. It makes the audit log the core concept for system state transitioning. That means the system is actually an audit log in the first place instead of representation of the current state. And from this log the current state of the application can be computed, much like the account balance is computed from the list of account entries. 

This computed value would be called a projection. It may be viewed as a function over list of events which returns a projection based on that list of events.

** pokus o matematickou fci?? **

The advantage of this design is that we can compute many variations of projections to suit are needs - we can populate the application state in a relational database, or create a search index for our data, etc. Since we store all events that happened in the system in our audit log, we can add a new projection at any time by simply processing all the historic events through a system that generates the new projection.

The other design pattern described in this chapter is the Command Query Responsibility Segregation (CQRS) design pattern. First of all, it is needed to say that neither one of the two patterns implies the other; they can work on their own. However, they can nicely complement each other to form a very effective and powerful design for your system.

Foundation for CQRS pattern is the idea that the system making writes and reads using the same data model is discouraged and dividing these responsibilities simplifies the design and easily enables horizontal scaling of the read side. It extends the idea of the CQS (Command Query Separation) principle for designing interfaces of objects. CQS says that the object's methods should be either commands or queries. The responsibility of a query is to only return data and not to alter the state of the object. On the other hand, a command should only change the state of the object but never return any data. **citation needed**

CQRS takes this idea and applies it to the system level where it strictly separates the responsibility for handling a command input (to change the application state) from the responsibility of side-effect-free reading of application data. **citation needed*

Both design patterns were popularized by the practitioners of the Domain-Driven Design (DDD) **citation needed** methodology for software development. DDD is a very comprehensive subject on its own and as such it will not be discussed in this thesis apart from the terms and principles that are involved in the presented design patterns.



