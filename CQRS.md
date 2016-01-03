## Command Query Responsibility Segregation

This section is intended to provide information about Command Query Responsibility Segregation (CQRS), the problems it aims to solve, and its connection to Event Sourcing.

The foundation for the CQRS pattern is the idea that the system making writes and reads using the same data model is discouraged and dividing these responsibilities both simplifies the design and easily enables horizontal scaling and increases performance. It extends the idea of the CQS (Command Query Separation) principle for designing interfaces of objects. CQS says that the object's methods should be either commands or queries. The responsibility of a query is solely to return data and not to alter the state of the object. On the other hand, a command should only change the state of the object but never return any data. **citation needed**

CQRS takes this idea and applies it to the system level where it strictly separates the responsibility for handling a command input (to change the application state) from the responsibility of side-effect-free reading of application data**citation needed**[journey]. You can see the conceptual view of the CQRS design in Figure X**reference needed**.

