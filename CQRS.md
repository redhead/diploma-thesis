## Command Query Responsibility Segregation

This section is intended to provide information about Command Query Responsibility Segregation (CQRS), the problems it aims to solve, and it's connection to Event Sourcing. The CQRS pattern was already briefly described in the introduction of this chapter. In its entirety, the idea is as simple as separating the command input handling to mutate data and querying the application to return the data. If each of the two concerns are separated to different modules, it enables us with all sorts of 

### Rationale

There are few reasons to consider this approach. First is that reading the application data (querying) is not something that needs transactional invariants to be involved; that is something commands need to address. A query just returns data. On top of that, queried data usually are represented in form of screens (or, based on your use cases, other types of interface) that users are presented with, so the perception of queried data can be different than the model that is used to alter the data. 

The second more important thing is that usually it is the read side of your application that needs to be scaled to ensure the quality of your system for a bigger audience. In many systems a ratio of reads to writes can be several orders of magnitude (**cite: http://codebetter.com/gregyoung/2010/02/20/cqrs-and-cap-theorem/ **). Instead of scaling everything in the system it is better to focus optimization by scaling the part that is most heavily used, i.e. the read side.

This leads us to an interesting aspect of most queries and that is relaxed consistency. In round-trip based web applications, users already interact with stale data in their browsers. Imagine the time that a response takes to return from a server to a user and the time the user spends viewing the screen before making interaction. At any point the data could have been mutated on the server and so, the user sees data that is inconsistent with the server. So, relaxing the consistency of queried data, so it can be easily scaled, is not a big issue. The important thing to mention here is that relaxing the consistency does not mean that the data is inconsistent on the read-side. It is rather **eventually consistent**. That means that correct data is returned by the query at some point. The delay before the read side is updated is not something that would break the system anyway.

This however does not apply to the write side where it is necessary to always operate on the current state to reliably ensure full consistency. This means that the write side is a lot harder to scale and be consistent at the same time. But if number of reads is more than one order of magnitude larger than number of writes then scaling the read side is not always needed.

As a final note on reasons for using CQRS, let's mention that it enables the application to easily have different types of read-side models running in parallel. In many cases, the application needs to query its data from different types of data models to function well. As an example, a fulltext search is not something very efficient to do in a relational database. The same applies to querying data structured as graphs. There is not a single model that does everything right; some models do something better than others in specific circumstances. CQRS together with Event Sourcing enables us to create multiple read models of different types very easily.

### Terminology

Before we get into implementing CQRS and Event Sourcing in Java it is needed to explain some of the terminology used in the process. In **citation needed CQRS Journer**, the CQRS pattern was presented as hugely influenced by the approach to developing software systems known as the Domain-Driven Design methodology (DDD). The DDD approach describes a set of techniques to analyze the domain of the system and to construct a conceptual model from the results of that analysis. This model is then used for designing large and complex domains and helps you solve the problems in the domains.

However, it is essential to say that DDD does not dictate the usage of CQRS, nor CQRS needs DDD for it to be implemented in the system. In much of the existing literature though, these two principles are used together and many CQRS practitioners use the terminology of DDD.**citation needed CQRS Journer**

Because of that, let us succinctly describe some of the DDD concepts and terminology that are usually involved in the CQRS pattern in the following paragraphs. You can find more information about DDD in **cite: DDD Evans**

#### Domain model

The core concept of DDD is the domain model. This model is built by the domain experts and software developers who both form the team responsible for developing the system. The model is used to capture all of the relevant domain knowledge and to determine scope and verify the consistency of that knowledge. The domain is expressed in code and is constantly maintained to reflect the changes as they occur in the evolution of the domain knowledge.

DDD is focused primarily on the domain because that is where the value of the software lies from the business point of view. The role of the domain model is to capture what is the valuable and unique to the business. Domain models are typically composed of entities, value objects, aggregates, and described using terms from a ubiquitous language.

#### Ubiquitous language

Many problems in real-world software development arise from different language nuances used by the software developer and the non-technical domain experts when they communicate between each other. The concept of ubiquitous language aims to create a dictionary that is strictly used when talking or writing about the domain. If both parties use the same language to talk about and to codify the domain to the domain model the risk of confusion or misunderstanding is reduced. DDD also leverages that software developers are not just coding monkeys and they should participate in constructing the domain model together with domain experts. 

#### Entities and value objects

Two of the main building blocks that make up the domain model are entities and value objects. **Entities** are objects that have their own identity that continues through time. Entities may have attributes that can change over time, but their identity stays unique in the system (for example an application user). On the other hand, **value objects** are defined by the values of their attributes and not by their identity. For example a customer's address is made up of street, city, state, and so on, but for the purpose of that domain, it's not necessary to make that address an entity with unique identity. Rather the values of the given attributes are what is important for value objects. Usually, value objects are immutable, and if the its attribute is to change the value object is replaced with a new instance. The difference from entities is that two entities might be equivalent in all their attributes, but will still be distinct.

#### Aggregates

An aggregate is a unit of transaction boundary. In DDD terms it is a cluster of related entities and value objects that are supposed to remain consistent within the system. The lifetimes of its components are bounded by the lifetime of the entire aggregate. An aggregate is responsible for consistency and upholding the invariants (business rules) that are set on its components by the domain. Also, aggregates should not hold references to other aggregates as they should not reach outside their consistency boundaries.

#### Aggregate roots

Since the aggregate is effectively a tree or a graph of objects, an aggregate root is the object at the top. The aggregate root is responsible for communication with and delegation to its child objects and thus it is the only entry point to the aggregate's objects from the outside world. Assertion of invariants (business rules) is also its responsibility.

#### Bounded context

All of the terms described above are used to create, maintain, and manage a domain model. Using a single domain model for large systems may be impractical, reducing consistency and comprehension. So, DDD adopts a concept of bounded context. A bounded context is a context for single domain model. This allows us to create multiple small domain models, each focusing on some group of functionality of the whole system.

### The CQRS concepts

Now that the necessary terminology of Domain-Driven Design was established, let's introduce CQRS-specific concepts and terms. Please note that even though DDD and CQRS are not implying one another they are usually used together in most of the literature. Thus the distinction of what is part of DDD and what is part of CQRS sometimes blends uncertainly. Some of the terms described below can be used to talk about DDD too.

#### Commands


#### Command Handlers

#### Events

#### Event Handlers

#### Sagas and process managers

#### Task-based UI
