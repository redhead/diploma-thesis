## Effects of CQRS and ES on achitecture

To be accurate, neither CQRS nor ES are patterns that could supersede the layered architecture (or any other), because they are very simple principles in the core, and not architectural designs per se. With that being said, layers are a good practice to hold to in any case. However, CQRS and ES have a big effect on the traditional architecture and design process.

#### Domain centric

One of the most visible changes is the focus of development. In traditional layered architecture, it is the database and structure of the data that is in the center. The service layer with business logic is then applied on top of it in a separate layer. In CQRS (written in DDD style), the domain model is the core of development effort, usually driven by use cases and problems in the domain. Of course, to act upon the domain model, it still needs some data access layer to get aggregate instances (or, in case of ES, events), but the dependency is now reversed. The business logic does not depend on the data access layer. This has a number of benefits, e.g. it makes it a lot easier to test the business logic, and also it is not effected by leaky abstraction of the data access layer.

This pushes the design more to the hexagonal**citation needed**[http://alistair.cockburn.us/Hexagonal+architecture] or clean**citation needed**[https://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html] architecture, which puts the domain and business logic into the center. This also turns the domain away from the anemic model that was often found in the traditional design.

#### Separation of concerns

In traditional layered architecture, a request for a query always needs to go through the business layer to get to the data access layer, even though it does not do anything business-specific and is free of side effects. In CQRS, the domain is not bloated with presentation issues, because the read model is built by event handlers with its own data access objects completely outside of this layer. This reduces the amount of code needed to implement queries and removes the pure delegation of methods between layers.

#### Loose coupling

Commands and events also greatly reduce coupling between layers. A command represents a request for some task to be done, but it does not depend on any concrete component that executes the task (except for the command bus that dispatches the command). The same applies to events, where event listeners are completely decoupled from publishers.

#### Scalability and performance

The clear separation of read and write models and the way the components are decoupled opens the way to increase scalability and performance. It is possible to easily include queues and messaging buses to the system to provide partitioning of command and event processing using asynchronous execution. This in turn allows for scaling the read and write models independently of each other. As stated in the description of CQRS, the read model can be *eventually consistent* and denormalized to views, which causes the application to perform better.

#### Testability

Layered architecture is testable, but it requires a lot of mocking and set up, because of coupling. For example, business logic needs to mock data access layer to instantiate and retrieve the domain objects (entities) and then the tests asserts the state of entities being saved back to the data access layer.

In CQRS, the components are nicely decoupled and driven by events and commands. This makes unit testing far more easy. In case of event sourcing, where the domain events are the main source to build state, we can express test scenarios purely in terms of events.