### Suggestions

As stated above, the design patterns are not easy to master in a short notice. Since this was the first time I used the presented design patterns in a project, not everything turned out to be good after refactoring. In the following paragraphs, a few corrections and improvements are suggested for the future development.

#### Create multiple bounded contexts

During the refactoring, the importance of bounded contexts, a way of separating unrelated responsibilities in the domain was underestimated. In the end, it turned out that the system is actually made of smaller domain models that could be separated. This also includes the aggregates which can be broken into smaller consistency boundaries that relate to different contexts. As Domain-Driven Design \cite{ddd} teaches us, the domain model should be incrementally improved as our knowledge about the domain increases.

The following list suggests an idea of how to break the current domain model into smaller bounded contexts.

- **File system bounded context** -- all the aggregates that are part of the simulated file system can be placed here
	- Files
	- Folders
	- Users (in the sense of an owner of the nodes)

- **User management bounded context** -- the processes dealing with user management
	- Registration
	- Forgotten password

- **Administration bounded context** -- the domain of system administration by super users
	- Organizational Units and their quotas
	- User roles
	- User administration

- **User settings bounded context** -- everything that can be set by a user
	- Labels
	- User groups


#### Do not force CQRS and Event Sourcing

As stated multiple times in the previous text, CQRS and Event Sourcing are not all or nothing design patterns. In some cases or some bounded contexts, they cause more harm than good. Take for example, the user password management --- users' old passwords (their hash values) should not be stored in the event log because it creates a major privacy and security risk. This is where Event Sourcing is not very beneficial. 

As stated in the paragraphs about bounded contexts above, the user password management could be placed into a different aggregate, which is not event-sourced. Thus, user details could still be event-sourced, but the user password would not. 

In some bounded contexts, where a domain model is trivial, a simple CRUD system could be used instead of forcing CQRS, e.g. in the administration bounded context. This technique was used in \cite{journey}. Note that all the changes still need to be validated against the domain knowledge, if it is truly inappropriate to use CQRS or ES. Investigate all the benefits and disadvantages that arise from not using the patterns before refactoring.

#### Denormalize read models

The strategy of the refactoring was to preserve the original relational database model, which would be used for querying. For now, the database doesn't cause any problems. If the database or the particular model does not suffice in the future, it is possible to create denormalized data from the domain events to incorporate different read models or database engines. In any case, the CQRS and ES enable the system to grow and scale in an optimized way. 

#### Snapshotting

Event sourcing is now used in all the aggregates without snapshotting. If system writes (i.e. command processing) become slow, it may be caused by too many events being queried and handled to get the aggregate instance to the desired state before handling the state change. According to Greg Young, the author of event sourcing, aggregates do not need snapshotting if the number of events of an instance is in hundreds \cite{greg-youtube}. If the number of events increases to thousands, snapshotting is a good way to optimize the write model. For more information about snapshotting in Axon Framework, see the Axon documentation \cite{axon-docs}.

#### Specialized event store

Events are now stored in the same database as the read model --- a PostreSQL database. This may not be a great choice for an event store because it is not optimized to do this task, and what's more, it is harder to implement publish-subscribe event processing on top of it. My recommendation is to search for a different database engine to store the events to. Particularly, EventStore \cite{eventstore} and Apache Kafka \cite{kafka} seem to be great candidates as they refer to event sourcing and processing in their overview and documentation.