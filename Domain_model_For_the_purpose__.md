## Domain model

For the purpose of the refactoring to CQRS and Event Sourcing it is needed to describe the current domain model of the back-end application. Please note that the domain model here depicts the model in a traditional UML domain model diagram. It is not meant to describe the domain model in terms of Domain-Driven Development. 

The domain model clearly captures all the entities and their relationships within the business domain. The knowledge of the original entities is important for the purpose of refactoring to CQRS where the entities must be rethought in terms of aggregates.