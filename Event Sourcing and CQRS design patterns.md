# Event Sourcing and CQRS design patterns

In this chapter, the core ideas of Event Sourcing (ES) and Command Query Responsibility Segregation (CQRS) design patterns are described. The basic principles and terms of these patterns are presented to the reader so they acquire enough information to understand the next chapters.

## Introduction to Event Sourcing and CQRS

It is not uncommon in software development world that principles make their way into every day usage long time after they were published or examined by their creators. Take for example functional programming, principles of which were described in 1930s, implemented into Lisp programming language in 1950s but never being part of mainstream software development until the start of 21<sup>st</sup> century where a boom of new functional programming languages happened.

The Event Sourcing design pattern took a similar path. The core idea of Event Sourcing has been used for ages but not so much in typical software development. You can get the idea of this pattern from bookkeeping or bank accounts where everything is stored as a list of accounting entries, or entries of money deposit and withdrawal. When you get your bank statement, you can see it is presented as a list of entries specifying what happened to your account each time a transaction happened and how much money was involved in each transaction. If you take all these entries and sum them (positive values for money deposit, and negative for withdrawal) you get your total account balance. This is how banks worked and are still working - they keep all these entries as a history of account transactions and by summing them we get the account balance.

Now, let's get back to software development. A traditional software design that you can find in many books and online tutorials usually stores things by keeping only the current state of the application in a relational or document database. This may seem as something that is really needed from the system because the users of the application usually want to see the current data and interact with it. However, by keeping only the current state (balance of a bank account) we lose a lot of valuable historic data. We lose the information about the time a state change happened, who invoked the change, and so on. This historic data may remind you of something called an audit log. 

An audit log is the very same thing as the bank statement from the accounting example. It is effectively a log of events that happened to the account in its entire existence. Many systems actually need an audit log (banking systems by their nature do) for the purpose of regulation or auditing. But in many systems, an audit log is a separate module called by the code that is handling a state change. However, this way may be error-prone as developers who make changes to the code must be very disciplined and know to call the audit log to store the correct change into the log. As the system grows bigger, it may take a lot of effort to maintain such audit log and more importantly prove its correctness!

Event Sourcing takes an approach that makes the audit log the core concept of the system. That means the system is actually an audit log in the first place instead of representation of the current state. And from this log the current state of the application can be computed, much like the account balance is computed from the list of account entries. This computed value is called a projection. 

The advantage of this design is that we can compute many variations of projections to suit are needs - we can populate the application state in a relational database, or create a search index for our data, etc. Since we store all events that happened in the system in our audit log, we can add a new projection at any time by simply processing all the historic events through a system that generates the new projection.

The other design pattern that we describe in this chapter is the Command Query Responsibility Segregation (CQRS) design pattern. First of all, it is needed to say that neither one of the two patterns implies the other; they can work on their own. However, they can nicely complement each other to form a very effective design for your system.

Foundation for CQRS pattern is the idea that the system making writes and reads using the same data model is discouraged and dividing these responsibilities simplifies the design and easily enables horizontal scaling of the read side. It extends the idea of the CQS (Command Query Separation) principle for designing interfaces of objects. CQS says that the object's methods should be either commands or queries. The responsibility of a query is to only return data and not to alter the state of the object. On the other hand, a command should only change the state of the object but never return any data.

CQRS takes this idea and applies it to the system level where it strictly separates the responsibility for handling a command input (to change the application state) from the responsibility of side-effect-free reading of application data.

## Event Sourcing

In the paragraphs above, the Event Sourcing design pattern was briefly introduced. The following text describes the design pattern in more detail. First, few reasons why use Event Sourcing is presented with some examples of where ES is an advantage for your system.

### Rationale

As already mentioned, the idea of event sourcing is not new. Various relational database management systems (RDBMS) found its way to most software applications as permanent storage mechanism, in which the application stores its current state in tables. However, an interesting aspect of these database systems is that, internally, they usually keep some kind of a transactional log where they store all the changes that were applied to the database. This log is then used, for example, to handle database transactions and replication of the data to slave nodes. It is very easy to replicate data if they are represented as immutable events about the change in a append-only log. To replicate, you just send the events that are new since the last replication and the receiving node applies these changes to itself, making both nodes synchronized.

Event Sourcing follows this idea - instead of storing the current state of the application, it primarily stores facts about changes (events) that happened to the application. The current state is degraded to be transient, meaning that we can throw it away and build it again just by processing all the events one by one. The benefits of this design is that we automatically get a correct audit log (in some cases required by the law), and a way to build the current state by making projection(s) of the events. 

The use of Event Sourcing can give you many beneficial use cases that would not be possible without it. The next paragraphs describe some of these cases for you to consider:

#### Rebuilding the state model

ES enables us to make projections of the events to a new state model. This can be a read model used by the application to query data to be presented to users. On top of that, it allows to rebuild the model to a completely different structure. We can change the way the user sees the data by projecting the events again but structuring the model differently. We can also use the existing events to build a separate read model that will exist next to the original one. For example, a search index of the application data stored in a full-text search server.

#### Debugging

We can use the event log to examine a fault in our system. Imagine that a user reports a bug without specifying the steps to reproduce the bug. This usually is a nightmare for software developers isolating the bug. But with Event Sourcing we have the whole history of events applied to the application stored in a log. Thus, we can go back in time by replaying the events (similarly as in rebuilding the model) and see what user did, by looking at the events, at the exact time the bug happened. We can than use standard debugging tools to see what code got executed by the application so it produced the bug. 

#### Fallback after a failure

Imagine you deploy a new version of your application for the users. In some cases this also means that you need to update your RDBMS tables with new a new schema. The system then works for a week and then it crashes. With ES you can go back to your old working code (if you use something like versioning systems), but also get the old data back before the schema update (or other destroying changes). You just need to recreate the original data model by replaying the events.

#### Event processing

With a complete event log, we can make projections of the data for the users. But we can also create other systems around the same event log. The systems can use some or all the events to do tasks like

- Monitoring (e.g. some events sent too often)
- Fraud detection
- Data analysis
- Data mining

Basically, any task that is usually involved in event-based systems.

#### New business requirements

One of the best examples, in my opinion, why use ES is that we can easily fulfill new business requirements that involve old data. Imagine that the project stake holders come up with a new requirement for an already working online shopping system. The feature they want you to implement is a suggestion mechanism that presents the user the products he or she removed from their cart just before the checkout. Possibly, the users wanted to buy these products but they removed it from the cart because the total order price was too high. The users may want to buy the products next month though. To suggest the user those products on their next visit we need the information that the products were removed from the cart just before the checkout. If we designed our domain events right we have that information for every user in the system already stored as events in the event log. We can build this feature easily using the old events and suggest the right products to each user right after the feature deployment. This is a very nice advantage over the systems that store only the current state because these systems do not have the data beforehand. The system wouldn't be able to suggest anything until a next user removes a product from their cart.

Very similar example of a requirement that involves historic data is a chart of price development for a product over time that many online shopping systems present to users. It is not possible (immediately) in the application that stores only the current state (the current price of the product). In an event sourced system, this is easy as we probably already have all the events about the price updates for every product.


### Domain Event


### Event Stream

### Projections

### Snaphotting

### Replaying the events


## Command Query Responsibility Segregation

### Aggregates

### Commands and Commands Handlers

### Events and Event Handlers

### Sagas

