### Managing complex business transactions

In many cases, an atomic and consistent transaction isn't possible to carry out, for example in integration with external systems. This is the case where a coordinator that mediates this kind of business transaction is needed and that can take compensating actions to resolve the failure in transaction, if necessary. In CQRS, it is called a saga or process manager. Axon provides an infrastructure to support sagas.

#### Saga

A saga is a special type of event listener that manages a business transaction. Each transaction is handled by one instance of a saga. It maintains a state needed to manage the transaction. Typically, a saga has a start and an end, both triggered by events. All sagas must implement the `Saga` interface.

Axon provides an implementation of the interface, called `AbstractAnnotatedSaga`, that uses annotations to extend the behavior of the saga instance. Similarly to annotated event handlers, there is the `@SagaEventHandler` annotation that marks the event handler method in a saga. There is also `@SagaStart` annotation that signifies that the handler method is the starting point of new saga instance.

On the other hand, the `@SagaEnd` annotation on an event handler method causes the saga instance to end. Alternatively, there is the `end()` method, that can be used to programmatically end the saga, for example when some condition is fulfilled.

To save resources when publishing events to sagas, they can be associated with certain properties. Only those events having the properties of given value, which a saga instance is associated with, are published to that saga instance. This way, the existing sagas won't be flooded with all the published events, but just those that matter to each saga instance. The association properties are specified by the `@SagaEventHandler` annotation.

#### Event Scheduler

Axon also supports scheduled events suitable for managing deadlines. Imagine a long-running transaction that involves paying an invoice. The deadline for paying an invoice can be several weeks. So, a saga can schedule an event for publication in the future. When the time comes the event is published and that triggers the associated event handler which handles hitting the deadline. 

The `EventScheduler` can be used to schedule events for publication. A simple in-memory implementation by the `SimpleEventScheduler` is suitable for non-critical short deadlines. When JVM is shutdown, the scheduled event is lost. For more distant deadlines, the `QuartzEventScheduler` can be used. It uses Quartz as the underlying scheduling mechanism, which provides persistence, clustering, misfire management and so on.

#### Saga Manager

The purpose of a saga manager is to route events to the appropriate saga instances and to manage their life cycle. All saga managers must implement the `SagaManager` interface. The `AnnotatedSagaManager` is an implementation that uses annotations on the annotated sagas to manage their life cycle and event routing. A list of saga types is needed configure the manager, so it knowns what sagas it administers. Also a saga repository must be provided to the manager.

#### Saga Repository

The responsibility of a saga repository is to store and retrieve saga instances. As some sagas can run for a very long period of time, it is reasonable to store the saga instances to a persistent storage. In case of JVM shutdown (e.g. on application restart or failure), the sagas are safely stored and ready to load when needed. Axon provides a number of implementations that handle the responsibility with different backing storages - JPA, JDBC and Mongo. The default saga repository is a simple in-memory implementation. When this repository is used, a saga is lost if JVM is shut down before the end of the saga.
