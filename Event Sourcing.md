## Event Sourcing

In the paragraphs above, the Event Sourcing design pattern was briefly introduced. The following text describes the design pattern in more detail. First, few reasons why use Event Sourcing is presented with some examples of where ES is an advantage for your system. Finally, core terms involved in the Event Sourcing design pattern are described.

### Rationale

As already mentioned, the idea of event sourcing is not new. Various relational database management systems (RDBMS) found its way to most software applications as permanent storage mechanism, in which the application stores its current state in tables. However, an interesting aspect of these database systems is that, internally, they usually keep some kind of a transactional log where they store all the changes that were applied to the database. This log is then used, for example, to handle database transactions and replication of the data to slave nodes. It is very easy to replicate data if they are represented as immutable events about the change in a append-only log. To replicate, you just send the events that are new since the last replication and the receiving node applies these changes to itself, making both nodes synchronized.

Event Sourcing follows this idea - instead of storing the current state of the application, it primarily stores facts about changes (events) that happened to the application. The current state is degraded to be transient, meaning that we can throw it away and build it again just by processing all the events one by one. The benefits of this design is that we automatically get a correct audit log (in some cases required by the law), and a way to build the current state by making projection(s) of the events. 

The use of Event Sourcing can give you many beneficial use cases that would not be possible without it. The next paragraphs describe some of these cases for you to consider:

#### Rebuilding the read model

ES enables us to make projections of the events to a new state model. This can be a read model used by the application to query data to be presented to users. On top of that, it allows to rebuild the model to a completely different structure. We can change the way the user sees the data by projecting the events again but structuring the model differently. We can also use the existing events to build a separate read model that will exist next to the original one. For example, a search index of the application data stored in a full-text search server.

#### Debugging

We can use the event log to examine a fault in our system. Imagine that a user reports a bug without specifying the steps to reproduce the bug. This is usually a nightmare for software developers isolating the bug. But with Event Sourcing we have the whole history of events applied to the application and stored in a log. Thus, we can go back in time by replaying the events (similarly as in rebuilding the model) and see what user did, by looking at the events, at the exact time the bug happened. We can than use standard debugging tools to see what code got executed by the application and why it resulted in producing the bug. 

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