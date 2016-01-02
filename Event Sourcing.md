## Event Sourcing

In the paragraphs above, the Event Sourcing design pattern was briefly introduced. The following text describes the design pattern in more detail. To help understand the idea of event sourcing, some necessary definitions are presented. Finally, a few reasons why use Event Sourcing is discussed with some examples of where ES is an advantage for applications.

Event sourcing is a way of persisting the state of an application by storing the history that shaped the application's current state. Instead of storing only the current state in some fixed structural model, e.g. in a relational database, event sourcing stores a list of events that happened in the system in its entire lifetime.

### Domain event

The core term in event sourcing is a domain event, which is usually regarded as just an event. A domain event is a fact about application state transition. In other words, it describes something that has happened to the system and resulted in some state change. In most cases, there are a number of different kinds of events in the system, each describing a different type of the change, e.g. `product added to cart` or `product reserved` events could fit in some online shopping application. Events have a single source that publishes the event and one or more receivers. Because events describe something that has already happened, they are immutable --- they cannot be changed or undone. But there can be subsequent events that can alter or cancel the effects of the previous events, e.g. `reservation cancelled`.

Usually, events are represented by simple objects with properties that store the parameters that describe the particular event. For example of a domain event represented by a Java class, see Listing X **listing needed**.

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

To make use of the event for the purpose of event sourcing, the developers need to create an instance of that class with appropriate parameters. The data contained in the object describe the properties of the particular event --- a product X of count Y was added to cart Z. 

Event names should reflect their intents from a business point of view. The `AddressCorrected` and `CustomerMoved` events reflect different business values in the domain, even though they would probably result in the same data change (updating the address).

The event in the example is named after the intent it represents as a verb in the past tense. This is important as the domain event symbolizes something that has happened in the past, a historic event that marks a state transition in the application. So, all events should follow this simple convention and be named as verbs in the past tense.

Another essential thing is that objects of this class are immutable as designed by the final fields and getter methods only. This is because the event has already happened and we can never change it, in the very same way we can't change our past in the real world.

### Event log

How events get generated will be described in the section about the CQRS design pattern. First, let's take a closer look at storing the events. As already mentioned, the events are saved to persistent storage in a representation called the event log, a list of events in the same order they have actually occurred in the system. Not only that, the event log is append-only, which means new events can only be added to the end of the log. This rule, together with the immutability of events, means that the already persisted list of events can never be changed or altered --- no inserting, editing, or deleting. The past cannot be changed.

The storage mechanism is not very important and it can differ in implementation. The storage can be an in-memory list (best suited for testing), a file system, a relational database, or storage specifically designed to persist a lot of messages in large quantities with great performance. It depends on the requirements and use cases to choose the best strategy for persisting events.

The event log is then used to recreate the current state of the application (or its parts). The exact way that this is carried out will be described in detail in the section about CQRS.

### Rationale

As previously mentioned, the idea of event sourcing is not new. Various relational database management systems (RDBMS) found their way to most software applications as a persistent storage mechanism, in which the application stores its current state in tables. However, an interesting aspect of these database systems is that, internally, they usually keep some kind of a transaction log where they store all the changes that were applied to the database**citation needed**[http://cqrs.nu/Faq/event-sourcing]. This log is then used, for example, to handle database transactions, crash recovery, and replication. It is very easy to replicate data if they are represented as immutable events in an append-only log. To replicate, a node just needs to send the events that are new since the last replication and the receiving node applies these changes to itself, making both nodes synchronized.

Event Sourcing follows this idea --- instead of storing the current state of the application, it primarily stores facts about changes (events) that happened to the application. The current state is degraded to be transient, meaning that we can throw it away and build it again just by processing all the events one by one. The benefits of this design are that we automatically get a correct audit log (in some cases required by the law), and a way to build the current state by making projection(s) of the events. 

The use of Event Sourcing can provide many beneficial use cases, that would not be so straightforward or possible at all without it. The next paragraphs describe some of these cases for the reader to consider.

#### Rebuilding the read model

Usually, there exists more than one way how to model and structure application data. More importantly, the models can change to accommodate new requirements.
Event sourcing enables us to make change or create new models from the events by making projections. This can be, for example, a read model used by the application to query data to be presented to users. On top of that, it allows us to rebuild the model to a completely different structure. We can change the way the user sees the data by projecting the events again but structuring the model differently. We can also use the existing events to build a separate read model that will exist next to the original one. Making a search index of the application data stored in a full-text search server is one example of another read model.

#### Fallback after a failure

When deploying a new version of your application into production for your users, in many cases it also means that you need to update your RDBMS tables with a new schema and/or update the data. Imagine that after such deployment the system works for only a week before it crashes because of serious failure. If fixing the system requires a lot of time you probably want to fall back to the last working version. 

If you use version control systems or other tools to back up your code base you can recover the working code easily. Getting back the database state, however, may be impossible because of the destructive changes made to the production database during deployment. The original data for the old version may be lost forever because of the schema update. If the database was backed up before the deployment you can get the original database back. However, you would lose all the data the users created during the week that the new version was working.

With Event Sourcing, this fallback can be accomplished easily. Since events must never be altered, we still have all the necessary data to recreate the original data model by replaying all the events including those created in that week.

But we can go a step further in our deployment process and keep the two systems, the original version, and the new version, running side by side. After deployment, the new version can take control of generating the events, while both versions can process them individually into their own data models in separate databases. If anything goes wrong with the live version, you can just switch back to the original (but still up-to-date) version running in parallel. This way the users will not be affected by long-lasting down times leaving you more time to fix the problem.

#### Event processing

With a complete event log, we can make projections of the data for the users. But we can also create other systems around the same event log. The systems can then use some or all the events to do tasks like:

- **monitoring** -- for the purpose of statistics regarding, for example, exceptional behaviour (e.g. frequency of events or unexpected order of events).
- **fraud detection** -- if a state that is considered a fraud by some specified rules is reached, the processor can notify interested people to act upon the situation.
- **data analysis** -- for inspecting the events to get some information valuable for the business, e.g. what action is done the most by the users at what time of a day.
- **data mining** -- to find some new trends and correlations in the data.
- **system integration** -- to merely communicate data between multiple systems.

#### Deriving business value from the event log

Event sourcing can help fulfill new business requirements that involve old data easily. Imagine that the project stakeholders come up with a new requirement for an already working online shopping system. The feature they want you to implement is a suggestion mechanism that presents the user the products they removed from their cart just before checking out. Possibly, the users wanted to buy these products but they removed them from the cart because the total order price was too high. The users may want to buy the products next month, though. To suggest those products to the user on their next visit we need the information that the products were removed from the cart just before checkout. If we designed our domain events properly we should have that information for every user in the system already stored as events in the event log. We can build this feature easily using the old events and suggest the right products to each user immediately after the feature deployment. This is a very nice advantage over the systems that store only the current state because these systems do not have the data beforehand. The system wouldn't be able to suggest anything until the next user removes a product from their cart.

A very similar example of a requirement that involves historic data is a chart of price development for a product over time. Many online shopping systems present this kind of chart to their users. It is not possible to (immediately) accomplish this requirement in an application that stores only the current state (the current price of the product). In an event sourced system, this task is easy to accomplish as we probably already have all the events with the price updates for every product.

#### Debugging

We can use the event log to examine a fault in our system. Imagine that a user reports a bug without specifying the steps to reproduce the bug. This is usually a nightmare for software developers isolating the bug. But with Event Sourcing, we have the whole history of events applied to the application and stored in a log. Thus, we can go back in time by replaying the events (similar to model rebuilding) and see what the user did, by looking at the events, at the exact time the bug happened. We can then use standard debugging tools to see what code got executed by the application and why it resulted in producing the bug.

Compare this feature with traditional logging. Logging is a way of saving events about the system execution in a textual, human-readable format to understand the activity of the system and to diagnose problems. However, this format is not very helpful if the log messages are not comprehensive enough, which is completely in the hands of the developers. Also it is less convenient to restore the system state from these messages because they are, unlike in event sourcing, disconnected from the code execution.

#### Testing

**TODO**

### Summary

As you can see, Event Sourcing isn´t a complicated idea at all. The premise is that we don't primarily save the current data of application state, but we instead save all the events that represent the state changes in the system in a persistent log. We then use this log as the single source of truth to determine the actual state of the application.

The concept, however, doesn't inherently say anything about how to get the state or how to see current data of an application. This is a job for the design pattern called CQRS, described in the next section.