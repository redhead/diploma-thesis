### The domain model

The entities of the original domain model remained to be used for building and querying the read model. However, the business logic, which was used to modify the entities, moved to the new domain model designed according to DDD principles by using Axon Framework and driven by commands.

The entities and their business logic in the original implementation were examined and rewritten to aggregates and their behavior. As an example, let's describe the transformation of one simple entity --- `Label`. Refactoring of the rest of the entities would follow the same scheme.

Labels can be assigned to (or removed from) folders and files (nodes) and they are owned by one user. A user can change a label's color and name, or delete it. Of course, by deleting, it the label is removed from all the assigned folders and files. To reference one concrete label (e.g. in the REST API), a unique ID is set to the entity.

In the original design, the `Label` entity was modeled as a Hibernate entity that exposed only getter and setter methods. The business logic was placed in the service layer, specifically to the implementation of `LabelService`.

This entity and the behavior can be easily transformed to an aggregate. The way that aggregates are designed follows the traditional non-anemic OOP principles, i.e. objects with both state and behavior. The `Label` aggregate would be modeled in UML like in Figure X **reference needed**. The fields that represented the original entity are specified in the aggregate as well and the business logic moved from the service layer to the aggregate.
