## Event Sourcing

The following text describes the event sourcing pattern in detail. First, to help understand the idea of the pattern, some necessary definitions are presented. Finally, a few reasons why use Event sourcing are discussed with some examples of where ES is an advantage for applications.

Event sourcing is a way of persisting the state of an application by storing the history that shaped the application's current state. Instead of storing only the current state in some fixed structural model, e.g. in a relational database, event-sourced model stores a list of events that happened in the system in its entire lifetime.



### Domain event

The core term in event sourcing is a domain event, which is usually referred to as just an event. A domain event is a fact about an application state transition. In other words, it describes something that has happened to the system and resulted in some state change. 

In most cases, there are a number of different kinds of events in the system, each describing a different type of the change. For example, "*product added to user's cart*" or "*product reserved*" events could fit in some online shopping application. Events have a single source that publishes the event (publisher) and one or more receivers that process the event. 

Because events describe something that has already happened, they are immutable --- they cannot be changed or undone in the very same way we can't change our past in the real world. But there can be subsequent events that can alter or cancel the effects of the previous events, e.g. "*reservation canceled*".

Event names should also reflect their intents from a business point of view. The "*address corrected*" and "*customer moved*" events reflect different business values in the domain, even though they would probably result in the same data change (updating the address).

As you've probably noticed, the events should be named in the form of a verb in the past tense. That is because the domain event symbolizes something that has already happened, a historic event that marks a state transition in the application.


### Event log

How events get generated will be described in the section about the CQRS design pattern. First, let's take a closer look at storing the events. As already mentioned, the events are saved to persistent storage in a representation called the event log, a list of events in the same order they have actually occurred in the system. Not only that, the event log is append-only, which means new events can only be added to the end of the log. This rule, together with the immutability of events, means that the already persisted list of events can never be changed or altered --- no inserting, editing, or deleting. **sidenote needed**

The actual storage mechanism is not very important and it can differ in implementation --- it can be an in-memory list (best suited for testing), a file system, a relational database, or a storage specifically designed to persist a lot of messages in large quantities with great performance. It depends on the requirements and use cases to choose the best strategy for persisting events.

The event log is then used to recreate the current state of the application (or its parts). The exact way that this is carried out will be described in detail in the section about CQRS.

### Rationale

The general idea of event sourcing has been used for ages but not so much in typical software development. As stated previously, most of the applications use the concept of current state that is usually persisted in, for example, a relational database. That is the reason why most developers tend to think about the state in a structural sense such as in Figure X**reference needed**. In an online shopping application, for instance, a model of a purchase order could look like that.
