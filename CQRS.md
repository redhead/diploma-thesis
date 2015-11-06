## Command Query Responsibility Segregation

This section is intended to provide information about Command Query Responsibility Segregation (CQRS), the problems it aims to solve, and it's connection to Event Sourcing. The CQRS pattern was already briefly described in the introduction of this chapter. In its entirety, the idea is as simple as separating the command input handling to mutate data and querying the application to return the data. If each of the two concerns are separated to different modules, it enables us with all sorts of 

### Rationale

There are few reasons to consider this approach. First is that reading the application data (querying) is not something that needs transactional invariants to be involved; that is something commands need to address. A query just returns data. On top of that, queried data usually are represented in form of screens (or, based on your use cases, other types of interface) that users are presented with, so the perception of queried data can be different than the model that is used to alter the data. 

The second more important thing is that usually it is the read side of your application that needs to be scaled to ensure the quality of your system for a bigger audience. In many systems a ratio of reads to writes can be several orders of magnitude (**cite: http://codebetter.com/gregyoung/2010/02/20/cqrs-and-cap-theorem/ **). Instead of scaling everything in the system it is better to focus optimization by scaling the part that is most heavily used, i.e. the read side.

This leads us to an interesting aspect of most queries and that is relaxed consistency. In round-trip based web applications, users already interact with stale data in their browsers. Imagine the time that a response takes to return from a server to a user and the time the user spends viewing the screen before making interaction. At any point the data could have been mutated on the server and so, the user sees data that is inconsistent with the server. So, relaxing the consistency of queried data, so it can be easily scaled, is not a big issue. The important thing to mention here is that relaxing the consistency does not mean that the data is inconsistent on the read-side. It is rather **eventually consistent**. That means that correct data is returned by the query at some point. The delay before the read side is updated is not something that would break the system anyway.

This however does not apply to the write side where it is necessary to always operate on the current state to reliably ensure full consistency. This means that the write side is a lot harder to scale and be consistent at the same time. But if number of reads is more than one order of magnitude larger than number of writes then scaling the read side is not always needed.

As a final note on reasons for using CQRS, let's mention that it enables the application to easily have different types of read-side models running in parallel. In many cases, the application needs to query its data from different types of data models to function well. As an example, a fulltext search is not something very efficient to do in a relational database. The same applies to querying data structured as graphs. There is not a single model that does everything right; some models do something better than others in specific circumstances. CQRS together with Event Sourcing enables us to create multiple read models of different types very easily.

### Terminology

Before we get into implementing CQRS and Event Sourcing in Java it is needed to explain some of the terminology used in the process. In **citation needed**, the CQRS pattern was presented as hugely influenced by the approach to developing software systems known as the Domain-Driven Design methodology (DDD). The DDD approach describes a set of techniques to analyze the domain of the system and to construct a conceptual model from the results of that analysis. This model is then used for designing large and complex domains and helps you solve the problems in the domains.

However, it is essential to say that DDD does not dictate the usage of CQRS, nor CQRS needs DDD for it to be implemented in the system. In much of the existing literature though, these two principles are used together and many CQRS practitioners use the terminology of DDD.**citation needed**

Because of that, let us describe some of the DDD concepts that are usually involved in the CQRS pattern in the following paragraphs. 

#### Domain Model

#### Entities and value objects

#### Aggregates

#### Aggregate Roots


### The CQRS concepts



### Commands and Commands Handlers

### Events and Event Handlers

### Sagas

