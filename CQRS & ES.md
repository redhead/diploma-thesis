## CQRS with Event Sourcing

In this section, let's revise what was said about CQRS (designed using DDD) and Event Sourcing and describe how the two concepts connect together.

CQRS in its core is as simple concept as separation of read and write models for purpose of scalability and performance in multi-user collaboration environment. Domain-driven design fits nicely to this paradigm with the domain model being the core of the CQRS write model. Commands and events then manage state transition and creation of read models respectively as well as communication and integration between aggregates and integration of other systems through messaging protocols.