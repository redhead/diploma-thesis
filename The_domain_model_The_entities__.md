## The domain model

The entities of the original domain model remained to be used for building and querying the read model. However, the business logic, which was used to modify the entities, moved to the new domain model designed according to DDD principles using Axon framework and driven by the commands.

The entities and their business logic in the original implementation were examined and rewritten to aggregates and their behavior. As an example, let's describe transformation of one simple entity - `Label`. Refactoring of the rest of the entities would following the same scheme.

Labels can be assigned to (or removed from) folders and files and they are owned by one user. User can change a label's color and name, or delete them. Of course, by deleting them the label is removed from all the assigned folders and files. To reference a concrete label (e.g. in the REST API), an unique ID is used.

This entity and the behavior can be easily transformed to an aggregate. The way that aggregates are designed follows traditional non-anemic OOP principles, i.e. objects with state and behavior. The `Label` aggregate would be modeled in UML like this:
