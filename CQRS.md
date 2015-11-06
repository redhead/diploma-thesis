## Command Query Responsibility Segregation

This section is intended to provide information about Command Query Responsibility Segregation (CQRS), the problems it aims to solve, and it's connection to Event Sourcing. The CQRS pattern was already briefly described in the introduction of this chapter. In its entirety, the idea is as simple as separating the command input handling to mutate data and querying the application to return the data. 

### Rationale

There are few reasons to consider this approach. First is that reading the application data (querying) is not something that needs transactional invariants to be involved; that is something commands need to address. A query just returns data. On top of that, queried data usually are represented in form of screens (or, based on your use cases, other types of interface) that users are presented with, so the perception of queried data can be different than the model to alter the data. The second more important thing is that usually it is the read side of your application that needs to be scaled to ensure the quality of your system for a bigger audience. In most systems the read/write ratio is not in balance and favors the read side. 

But first, it is necessary to introduce some basic terminology of CQRS.

In **citation needed**, the described CQRS pattern was hugely influenced by the approach to developing software system known as the Domain-Driven Design methodology (DDD). The DDD approach describes set of techniques to analyze the domain of the system and to construct a conceptual model of results of that analysis. This domain model than helps you solve the problems in the domain, and is well suited for designing large and complex domains.

However, it is essential to say that DDD does not dictate the usage of CQRS, nor CQRS needs DDD for it to be implemented in the system. In much of the existing literature though, these two principles are used together and many CQRS practitioners use the terminology of DDD.

Because of that, let us describe some of the DDD concepts that are usually involved in the CQRS pattern in the following section.

### Aggregates



### Commands and Commands Handlers

### Events and Event Handlers

### Sagas

