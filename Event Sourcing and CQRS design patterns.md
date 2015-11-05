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

The other design pattern that we describe in this chapter is the Command Query Responsibility Segregation (CQRS) design pattern. First of all, it is needed to say that neither one of the two patterns implies the other; they can work on their own. However, they can nicely complement each other to form a very effective and powerful design for your system.

Foundation for CQRS pattern is the idea that the system making writes and reads using the same data model is discouraged and dividing these responsibilities simplifies the design and easily enables horizontal scaling of the read side. It extends the idea of the CQS (Command Query Separation) principle for designing interfaces of objects. CQS says that the object's methods should be either commands or queries. The responsibility of a query is to only return data and not to alter the state of the object. On the other hand, a command should only change the state of the object but never return any data. **citation needed**

CQRS takes this idea and applies it to the system level where it strictly separates the responsibility for handling a command input (to change the application state) from the responsibility of side-effect-free reading of application data. **citation needed*

Both design patterns come from the Domain-Driven Design (DDD) **citation needed** methodology for software development. DDD is a very comprehensive subject on its own and as such it will not be discussed in this thesis apart from the terms and principles that are involved in the presented design patterns.

## Event Sourcing

In the paragraphs above, the Event Sourcing design pattern was briefly introduced. The following text describes the design pattern in more detail. First, few reasons why use Event Sourcing is presented with some examples of where ES is an advantage for your system.

### Rationale

As already mentioned, the idea of event sourcing is not new. Various relational database management systems (RDBMS) found its way to most software applications as permanent storage mechanism, in which the application stores its current state in tables. However, an interesting aspect of these database systems is that, internally, they usually keep some kind of a transactional log where they store all the changes that were applied to the database. This log is then used, for example, to handle database transactions and replication of the data to slave nodes. It is very easy to replicate data if they are represented as immutable events about the change in a append-only log. To replicate, you just send the events that are new since the last replication and the receiving node applies these changes to itself, making both nodes synchronized.

Event Sourcing follows this idea - instead of storing the current state of the application, it primarily stores facts about changes (events) that happened to the application. The current state is degraded to be transient, meaning that we can throw it away and build it again just by processing all the events one by one. The benefits of this design is that we automatically get a correct audit log (in some cases required by the law), and a way to build the current state by making projection(s) of the events. 

The use of Event Sourcing can give you many beneficial use cases that would not be possible without it. The next paragraphs describe some of these cases for you to consider:

#### Rebuilding the read model

ES enables us to make projections of the events to a new state model. This can be a read model used by the application to query data to be presented to users. On top of that, it allows to rebuild the model to a completely different structure. We can change the way the user sees the data by projecting the events again but structuring the model differently. We can also use the existing events to build a separate read model that will exist next to the original one. For example, a search index of the application data stored in a full-text search server.

#### Debugging

We can use the event log to examine a fault in our system. Imagine that a user reports a bug without specifying the steps to reproduce the bug. This is usually a nightmare for software developers isolating the bug. But with Event Sourcing we have the whole history of events applied to the application stored in a log. Thus, we can go back in time by replaying the events (similarly as in rebuilding the model) and see what user did, by looking at the events, at the exact time the bug happened. We can than use standard debugging tools to see what code got executed by the application and why it resulted in producing the bug. 

#### Fallback after a failure

When deploying a new version of your application into production for your users, in many cases it also means that you need to update your RDBMS tables with a new schema and/or update the data. Imagine that after such deployment the system works for only a week before it crashes because of serious failure. If fixing the system requires a long time you probably want to fall back to the last working version. 

If you use code versioning systems or other tools to backup your code base you can get the working code back easily, but getting back the database state may be impossible because of the destructive changes made to the production database. The original data for the old version may be forever lost because of the schema update. If the database was backed up before the deployment we can get the original database back but we would lose all the data our users created in that week the new version worked.

With Event Sourcing this fallback can be accomplished easily. Since events must never be altered, and this applies to version deployment too, we still have all the necessary data to recreate the original data model by replaying all the events including those created in that week.

But we can go a step ahead in our deployment process and keep the two systems, the original version and the new version, running side by side. After deployment, the new version can take control of generating the events that both versions can process individually into their own data models in separate databases. If anything goes wrong with the live version, you can just switch to the original (but still up-to-date) version running in parallel. This way the users will not be affected by long lasting down times leaving you space to fix the problem.

#### Event processing

With a complete event log, we can make projections of the data for the users. But we can also create other systems around the same event log. The systems can then use some or all the events to do tasks like

- Monitoring (some events sent too often, or unexpected order of events)
- Fraud detection
- Data analysis
- Data mining
- System integration

#### New business requirements

One of the best examples, in my opinion, why use ES is that we can easily fulfill new business requirements that involve old data. Imagine that the project stake holders come up with a new requirement for an already working online shopping system. The feature they want you to implement is a suggestion mechanism that presents the user the products he or she removed from their cart just before the checkout. Possibly, the users wanted to buy these products but they removed it from the cart because the total order price was too high. The users may want to buy the products next month though. To suggest the user those products on their next visit we need the information that the products were removed from the cart just before the checkout. If we designed our domain events right we have that information for every user in the system already stored as events in the event log. We can build this feature easily using the old events and suggest the right products to each user right after the feature deployment. This is a very nice advantage over the systems that store only the current state because these systems do not have the data beforehand. The system wouldn't be able to suggest anything until a next user removes a product from their cart.

Very similar example of a requirement that involves historic data is a chart of price development for a product over time that many online shopping systems present to users. It is not possible to (immediately) accomplish this requirement in the application that stores only the current state (the current price of the product). In an event sourced system, this is easy as we probably already have all the events about the price updates for every product.

### Domain Event

The core term in ES is a domain event, which was mentioned few times already as just an event. A domain event is a fact about application state transition. In other words, it describes something that has happened to the system and resulted in some state change. In most cases, there is number of different kinds of events in the system. Usually they are represented by simple objects with properties that store the data describing the particular event.

You can see an example of a domain event represented by a Java class in the listing #l **listing needed**

	public class ProductAddedToCart {
		
		private final ProductId productId;

		private final CartId cartId;

		private final int productCount;

        public ProductAddedToCart(ProductId productId, CartId cartId, int productCount) {
            this.productId = productId;
            this.cartId = cartId;
            this.productCount = productCount;
        }

        // ... getters ...

	}

If we create an instance of that class, the data contained in the object describe the particular event in the time - a product X of count Y was added to cart Z. 

Event names should reflect their intents from the business point of view. The `AddressCorrected` and `CustomerMoved` events reflect different business values in the domain, even though they probably may result in the same data change (updating the address).

The event in the example is named after the intent it represents as a verb in the past tense. This is important as the domain event symbolizes something that has happened in the past. A historic event that marks a state transition in the application. So, all events should follow this simple convention and be named as verbs in the past tense.

Another essential thing is that objects of this class are immutable as designed by the final fields and getter methods only. This is because the event has already happened and we can never change it in the very same way we can't change our past in the real world.

### Event Log

How events get generated will be described in section about CQRS design pattern. For now, lets take a closer look at storing the events first. As already mentioned, the events are saved to a persistent storage in a representation called the event log, a list of events in the same order they have actually happened in the system. Not only that, the event log is append-only, which means new events can only be added to the end of the log. This together with immutability of events means that already persisted list of events must never be changed or altered - no event inserting, editing, or deleting. The past cannot be changed.

The storage mechanism is not very important and it can differ in implementation. It is up to your use cases to choose the best strategy for persisting events. The storage can be an in-memory list (best suited for testing), a file system, a relational database, or a storage specifically designed to persist a lot of messages in very big numbers with great performance.

The event log is then used to recreate the current state of the application (or its parts). How exactly will be described in detail in section about CQRS.

### Summary

As you can see, Event Sourcing isn´t complicated idea at all. The premise is that we don't primarily save the current data of application state, but we instead save all the events that represent the state changes in the system in a persistent log. We then use this log as the single source of truth to determine the actual state of the application.

The concept however doesn't naturally say anything about how to get the state or how can we see current data of an application. This is a job for another design pattern called CQRS described in the next section.

## Command Query Responsibility Segregation

### Aggregates

### Commands and Commands Handlers

### Events and Event Handlers

### Sagas

