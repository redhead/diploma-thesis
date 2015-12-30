## Command Query Responsibility Segregation

This section is intended to provide information about Command Query Responsibility Segregation (CQRS), the problems it aims to solve, and its connection to Event Sourcing.

The CQRS pattern was already briefly described in the introduction of this chapter. In its entirety, the idea is as simple as separating the command input handling to mutate data and querying the application to return the data. If each of the two concerns is separated to a different module, it provides us some benefits described in the following section.

### Rationale

There are few reasons to consider splitting the two responsibilities. First is that reading the application data (querying) is not something that usually needs transactional invariants to be involved; that is something commands need to address. A query just returns data. On top of that, queried data are usually represented in form of screens (or, based on your use cases, other types of interface) that the users are presented with, so the perception of queried data on a screen can be different than the model that is used to alter the data. This model, sometimes called a write model, must be able to uphold transactional invariants or business rules of entities and it is usually subject to concurrent changes and/or multi-user collaboration.

The second more important thing is that usually it is the read side of your application that needs to be scaled to ensure the quality of your system for a bigger audience. In many systems a ratio of reads to writes can be several orders of magnitude (**cite: http://codebetter.com/gregyoung/2010/02/20/cqrs-and-cap-theorem/ **). Instead of scaling everything in the system it is better to focus on the optimization by scaling only the part that is most heavily used, i.e. the read side.

This leads us to an interesting aspect of most queries and that is relaxed consistency. In round-trip based web applications, users already interact with stale data in their browsers. Imagine the time that a response takes to return from a server to a user and the time the user spends viewing the screen before making interaction. At any point the data could have been mutated on the server by other users or the system itself and so, the user sees data that is inconsistent with the server. So, relaxing the consistency of queried data, so it can be easily scaled, is not a big issue. The important thing to mention here is that relaxing the consistency does not mean that the data is inconsistent on the read-side. It is rather **eventually consistent**; that means that correct and consistent data is returned by the query at some point, but it just may not be immediately. The delay before the read side is updated is not something that would break the system in most cases.

This however does not apply to the write side where it is necessary to always operate on the current state to reliably ensure full consistency. This means that the write side is usually a lot harder to scale and to ensure being consistent at the same time. But, depending on the type of your system, scaling the write side is not always needed.

As a final note on reasons for using CQRS, let's mention that it enables the application to easily have different types of read-side models running in parallel. In many cases, the application needs to query its data from different types of data models to function well. As an example, a fulltext search is not something very efficient to do in a relational database. The same applies when querying data that are structured as graphs. A single model that does everything right does not exist; some models do things better than others in specific circumstances. CQRS together with Event Sourcing enables us to create multiple read models of different types very easily.

### Terminology

Before we get into implementing CQRS and Event Sourcing in Java it is necessary to explain some of the terminology used in the process. In **citation needed CQRS Journey**, the CQRS pattern was presented as hugely influenced by the approach to developing software systems known as the Domain-Driven Design methodology (DDD). The DDD approach describes a set of techniques to analyze the domain of the system and to construct a conceptual model from the results of that analysis. This model is then used for designing large and complex domains and helps you solve the problems in the domains.

However, it is essential to say that DDD does not dictate the usage of CQRS, nor CQRS needs DDD for it to be implemented in the system. However, in much of the existing literature these two principles are used together and many CQRS practitioners use the terminology of DDD.**citation needed CQRS Journer**

Because of that, let us succinctly describe some of the DDD concepts and terminology that are usually involved in the CQRS pattern in the following paragraphs. You can find more information about DDD in **cite: DDD Evans**

#### Domain model

The core concept of DDD is the domain model. This model is built by the domain experts and software developers who both form the team responsible for developing the system. The model is used to capture all of the relevant domain knowledge and to determine scope and verify the consistency of that knowledge. The domain is expressed in code and is constantly maintained to reflect the changes as they occur in the evolution of the domain knowledge.

DDD is focused primarily on the domain because that is where the value of the software lies from the business point of view. The role of the domain model is to capture what is valuable and unique to the business. Domain models are typically composed of entities, value objects, aggregates, and described using terms from a ubiquitous language.

#### Ubiquitous language

Many problems in real-world software development arise from different language nuances used by the software developers and the non-technical domain experts when they communicate between each other. The concept of ubiquitous language aims to create a dictionary that is strictly used when talking or writing about the domain. If both parties use the same language to talk about and to codify the domain to the domain model the risk of confusion or misunderstanding is reduced. DDD also leverages that software developers are not just coding monkeys and they should participate in constructing the domain model together with the domain experts. 

#### Entities and value objects

Two of the main building blocks that make up the domain model are entities and value objects. **Entities** are objects that have their own identity that continues through time. Entities may have attributes that can change over time, but their identity stays unique in the system (for example an application user). On the other hand, **value objects** are defined by the values of their attributes and not by their identity. For example a customer's address is made up of street, city, state, and so on, but for the purpose of that domain, it's not necessary to make that address an entity with unique identity. Rather the values of the given attributes are what is important for value objects. Usually, value objects are immutable, and if its attribute is to change the value object is replaced with a new instance. The difference from entities is that two entities might be equivalent in all their attributes, but will still be distinct; in case of value objects, they would be considered equal.

#### Aggregates

An aggregate is a unit of transaction boundary. In DDD terms it is a cluster of related entities and value objects that are supposed to remain consistent within the system. The lifetimes of its components are bounded by the lifetime of the entire aggregate. An aggregate is responsible for consistency and upholding the invariants (business rules) that are set on its components by the domain. Also, aggregates should not hold references to other aggregates as they should not reach outside their consistency boundaries.

#### Aggregate roots

Since the aggregate is effectively a tree or a graph of objects, an aggregate root is the object at the top. The aggregate root is responsible for communication with and delegation to its child objects and thus it is the only entry point to the aggregate's objects from the outside world. Assertion of invariants (business rules) is also its responsibility.

#### Bounded context

All of the terms described above are used to create, maintain, and manage a domain model. Using a single domain model for large systems may be impractical, reducing consistency and comprehension. So, DDD adopts a concept of bounded context. A bounded context is a context for a single domain model. This allows us to create multiple small domain models, each focusing on some group of functionality of the whole system.



### The CQRS concepts

Now that the necessary terminology of Domain-Driven Design was settled, let's introduce terms that are more related to CQRS concepts. Please note that even though DDD and CQRS are not implying one another they are usually used together in most of the literature. Thus the distinction of what is part of DDD and what is part of CQRS sometimes blends uncertainly. Moreover, CQRS is to a greater degree a principle rather than a design pattern with its own terminology. However, the CQRS implementation established by using the domain-driven design comes with a new vocabulary. Some of the terms described below can be used to talk about DDD too and vice versa. **reference http://programmers.stackexchange.com/questions/302808/cqrs-and-ddd-terminology/302809#302809 **

#### Commands

A command in CQRS is a way of expressing user's intent for the system to do some action. The user of the system can be a real person or an automated system, but each one can send commands to the system to perform a task. For example, "Add product X to cart Y" or "Checkout". A command should clearly reflect its intent because some command's intent may have different invariant value than other commands. Thus, it's not very wise to create a simple "Update" command capable of updating anything, because it does not capture the intent of the update. This leads us to the term *task-based user interface* which is described further below. 

#### Command Handlers

A command is usually processed by a single recipient called a command handler. The responsibility of the command handler is to execute code to perform the action or task intended by that command. In a domain-driven application, the command handler should validate that the command is a valid command, then it should locate the aggregate instance that is the target of the command (or create a new aggregate if that's the command's intention) and call the appropriate method on the aggregate instance with parameters from the command. Finally, it persists the new state of the aggregate to storage.

#### Events

Events in CQRS are part of the messaging pattern and are a superset of domain events. With that in mind, we can use these events for communication and integration, whether that be between aggregates in the same or other bounded contexts or with other systems or subsystems. They have a special meaning for Event Sourcing which will be described later. Events are published to subscribers that pick the information that something has happened in the system.

#### Event Handlers

Events are published to multiple recipients called event handlers. Event handlers are subscribed for a particular type of event that the subscriber knows how to handle. Their job is to receive an event, get the target aggregate and call its appropriate method. Finally, it should persist the new state of the aggregate to storage. Some event handlers do not operate on aggregates at all and are used for messaging instead; see below.

#### Messaging

Both **commands** and **events** implement the messaging pattern that enables us to make a system driven by events (event-driven design). This pattern provides us with a lot of flexibility because the messages (commands and events) can be examined, prioritized, queued, partitioned, subscribed to, retried, forwarded and so on. In CQRS, events can be sent using some messaging protocol to other applications or subsystems in order to, for example, update the read model. On the other hand, commands can be prioritized or queued before they are actually sent to a command handler. This of course depends on the requirements for the system being developed.

#### Sagas and process managers

Saga and process manager are often used interchangeably when referring to a Process Manager. Both are strategies to handle long running business transactions without using distributed transactions. Often there is a situation where we need to coordinate communication between multiple aggregates. Take an order process for example, when order is placed we need to wait for the user to pay, meanwhile the products are reserved for the user. At the same time the order can be limited by time and can be rejected if the user doesn't pay within some time period. So a process manager acts as a mediator between the aggregates conveying messages based on rules given by the process it manages. Usually, it holds some internal state by which it decides to send new events or commands to aggregates.

#### Task-based user interface

In a typical three-layer architecture or simple CRUD (Creat, Read, Update, Delete) systems, the service layer translates its data models to DTOs (Data Transfer Objects) and passes them to the user in form of user interface (UI). The user modifies the data and sends it back to the service layer for persistence. The service layer updates its data models and stores the changes into the data store. This way, passing data objects by using the same update model badly captures the user's intent of the change and usually is in contrast of what domain-driven design teaches us how to reason about our domain. Commands in CQRS should reflect their intents, and the same thing can be applied to the UI as well. User interface can construct the commands to be sent to the write side. This term is not related purely to CQRS and is not required or needed. Sometimes a CRUD interface is all what is needed and task-based UI can cause unnecessary complexity. However, developing with DDD and CQRS usually leads to such UI design for complex domains.
