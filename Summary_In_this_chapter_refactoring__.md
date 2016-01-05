## Summary

In this chapter, refactoring of Integration Portal application to CQRS and Event Sourcing was described. This section summarizes the outcome of the refactoring and presents suggestions for future work.

The system was originally written in a strict n-layered architecture, where domain entities backed by Hibernate served the purpose of both writing and reading. Both responsibilities were mixed together in the service layer. Refactoring to CQRS leads to reducing the complexity by designating the business and write logic to another module, written in Axon Framework. Using this framework, the responsibility of the write model was to handle state changes (mainly by using Event Sourcing) and the read model was built by the events published in the process of changing state. To simplify the refactoring, the read model was preserved in the original design.

Let's now describe a few benefits, which were brought by the refactored version, as well as a few disadvantages. Furthermore, let's outline some ways how to leverage this design for the next development of Integration Portal.

### Benefits

Most of the benefits of the CQRS and ES designs that were described in \ref{event-sourcing-and-cqrs-design-patterns} \nameref{event-sourcing-and-cqrs-design-patterns} were not fully leveraged after refactoring. The reason is that these designs are so-called enabling patterns, that means they give the developers more options and tools to extend the functionality of Integration Portal in the future. New possible features that are based on the new design will be presented further in the text. For now, let's described the immediate benefits brought by the refactoring in the following paragraphs.

#### Reduced code complexity

The code that provides access to state transitions, business logic, as well as querying for data is centered in the service layer. The implementation of the service layer before the refactoring, however, was complex and hard to maintain. The dependencies and responsibilities of the individual service classes were not clear. The code of the methods usually mixed validation and business logic to update and query entities at the same time.

The refactoring, that followed the presented strategy, didn't bring clear read-write separation of the service layer to separate modules, as the general CQRS pattern recommends, but it greatly simplified the complexity of the service layer. Methods in the service layer now have only one responsibility --- to query the read model or to send a command to the write model. The query methods simply delegate queries to the DAO layer, whereas the command methods create a command with data and the target aggregate identifier and send it to the command gateway. The responsibilities and the dependencies are now visible and clear.Additionally, by implementing the CQRS in Axon Framework, the write model, and the business logic is now more transparent and focused in one place, the aggregates, whose changes are triggered by command handlers. On the other side, updates to the entities of the read model are put in separate classes, whose methods are triggered by event handlers when the state changes.

The responsibilities are now clearly divided into separate submodules. More importantly, the coupling of the responsibilities is seamless thanks to Axon Framework infrastructure, making it easier to maintain and test.
Because all the modules now have an explicit purpose, it is easier to reason about extending the code with new features in the sense of responsibility.

Even though the complexity and code entanglement at the lowest level was reduced by the refactoring, the code got more complex from the architecture point of view.

#### Commands and Events

Representing commands and events in the form of classes makes it easy for the developers to orient themselves in the use cases of the system. Each command carries a clear intent about what should happen in the domain, and each event distinctly announces to the listeners what actually happened, so they can appropriately respond.

Furthermore, commands can be intercepted to be enriched or changed before they are actually processed. For example, it was possible for the commands to be assigned a currently logged-in user required by the domain. This operation was dedicated to single class and reused seamlessly for many commands. In the original code, this wasn't possible, and the code to query for the logged-in user needed to be duplicated.

#### Old issues resolved

One of the business rules of the simulated file system is that two nodes (folders and files) must not have the same name under the same folder. Before the refactoring, there was an issue with this rule, where nodes could have the same name if they were located in the root of the file system. This bug was caused by the mechanism chosen to guard this uniqueness. The mechanism was a unique database index on the `name` and `parent` columns, where the parent was a foreign key reference to a folder entity. The problem was that when the parent was `null` (marking the root of the file system tree), the index didn't work. In the file system root, the node names could be duplicated.

Because CQRS handles denormalized data in the read model very well, a special table (and an entity) was created only to capture dependencies of nodes and their names without any other node properties. This table was queried to check if a name of some node is unique before any change regarding the name uniqueness. The table was intentionally kept very simple, so it does not use many resources when querying. More about the node name uniqueness rule was presented in \ref{dealing-with-consistency-issues} \nameref{dealing-with-consistency-issues}.

Following the refactoring, changes in the user management were easier to implement in the new design. Sagas and scheduled events are in the center of user registration, verification, and initial password set up, as well as restoration of forgotten password. By using Axon's sagas, these long-running business transactions were easy to implement.

#### Testing

Integration testing was simplified to only sending appropriate commands and asserting the state of the database. It is worth to mention that both the tests and the actual implementation use the same commands to get the system to a specific state. Ultimately, this means that the tests are driven by the use cases represented as a series of commands. This way, we can design our tests according to domain-specific rules and operations.

On top of that, by using CQRS and especially Event Sourcing, it is possible to express tests purely in terms of commands and events when testing aggregates in a domain. Since the input to an aggregate is a command and state transition of an aggregate is always an event, the complete test scenario can be defined using just these objects. They usually follow the scheme of:

- given certain events in the past
- when this commands executes
- expect some events to be published

The tests written this way are expressive and clear in their intentions. They can be viewed as a part of unit testing; however, since they are mainly driven by functional requirements, they hardly depend on any implementation details, making them less vulnerable to code changes inside the aggregates.