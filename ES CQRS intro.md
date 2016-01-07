# Event Sourcing and CQRS Design Patterns

In this chapter, the core ideas of Event Sourcing (ES) and Command Query Responsibility Segregation (CQRS) design patterns are described. The basic principles and terms of these patterns are presented to the reader so they acquire enough information to understand the next chapters.

## Introduction

The traditional software described in most books and online tutorials usually stores its current state in a structural model that is persisted to some kind of a database system, e.g. a relational database. The principles of Event Sourcing, that will be described further in the text, puts the idea of such current state aside. Together with the Command Query Responsibility Segregation design pattern, they have a number of interesting effects on application development and design, and may contribute to increasing the business value of an application. More information about the benefits will be covered in the following sections. 

It's appropriate to say that neither of the two patterns implies the other; they can work independently. However, they nicely complement each other to form a very effective and powerful design for a system.

Both design patterns were popularized by the practitioners of the Domain-Driven Design (DDD) \cite{ddd} methodology for software development. And so, the DDD concepts and ideas influenced the implementation details of the two patterns. DDD is a very comprehensive subject on its own and as such will not be discussed in this thesis apart from the terms and principles that are involved in the presented patterns.