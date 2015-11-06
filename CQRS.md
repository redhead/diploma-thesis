## Command Query Responsibility Segregation

This section is intended to provide information about Command Query Responsibility Segregation (CQRS), the problems it aims to solve, and it's connection to Event Sourcing. The CQRS pattern was already briefly described in the introduction of this chapter. In its entirety, the idea is as simple as separating the command input handling to mutate data and querying the application to return the data. But first, it is necessary to introduce some basic terminology of CQRS.

In **citation needed*, the described CQRS pattern was hugely influenced by the approach to developing software system known as the Domain-Driven Design methodology (DDD). The DDD approach describes set of techniques to analyze the domain of the system and to construct a conceptual model of results of that analysis. This domain model than helps you solve the problems in the domain, and is well suited for designing large and complex domains.

However, it is essential to say that DDD does not dictate the usage of CQRS, nor CQRS needs DDD for it to be implemented in the system. In much of the existing literature though, these two principles are used together and many CQRS practitioners use the terminology of DDD.

Because of that, let us describe some of the DDD concepts that are usually involved in the CQRS pattern in the following section.

### Aggregates



### Commands and Commands Handlers

### Events and Event Handlers

### Sagas

