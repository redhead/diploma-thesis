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
