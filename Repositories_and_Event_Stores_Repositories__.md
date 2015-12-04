### Repositories and Event Stores

Repositories are responsible for providing access to aggregates. In CQRS, they are part of the write model, because aggregates are used to handle state changes. For any other queries, a read model should be used. Typically, repositories are optimized for lookup of an aggregate by its unique identifier only. Some repositories will store the state of the aggregate itself (using ORM, for example), while others store the state changes represented as events in an Event Store. The repository is responsible for persisting the changes made to aggregates in its backing storage.

#### Repositories

In Axon, repositories must implement the `Repository` interface. This interface defines methods for persisting a new aggregate instance (the `add()` method) and `load()` methods for retrieval of previously saved instance. There are two `load()` methods, both of which use an aggregate identifier to load the desired aggregate instance. One of the methods, however, has an extra `version` parameter, that can be used to load an aggregate instance of specific version. For more information about the aggregate versioning, see CONFLICTING MODIFICATION**reference needed**.

For the purpose of event sourcing, Axon provides the `EventSourcingRepository` which loads an aggregate instance by replaying its historic events to get it to the desired state. Also, when new events are applied to an aggregate instance, as a result of state change during command handling, this repository is responsible for persisting the new events when committed. This all is done seamlessly without programmer's intervention.

#### Event Stores
The `EventSourcingRepository` must be configured with a storage mechanism that handles the actual loading and storing of the aggregate's events. This mechanism is called an Event Store and there are several implementations that can be used. The implementations differ mainly in the backing storage that is responsible for persisting the events, e.g. file system, Mongo database, JPA, JDBC, etc.


