## Refactoring the domain model

As described in INTEGRATION PORTAL**reference needed**, the original domain model consisted of Hibernate entities that were modified by the business logic in the service layer and persisted by the data access layer. The refactoring of functionality for each entity is described in the following paragraphs.

The side effect of carrying out the strategy outlined above is that most of the original queries to the system remained unchanged after the refactoring. This means that the way the data are queried is more or less the same as in the original design --- query the data access layer for the data stored in the database, then map the data to the respective DTOs so they can be converted to JSON and sent to the client.

The methods responsible for carrying out the business logic, however, were rewritten from scratch to follow CQRS principles. In the following text, the changes made to the code are described.

### Service layer

In the original design, the service layer was an interface used for both accessing the data and invoking the business logic. A few of the methods used both responsibilities mixed together --- the business logic made some modifications to the data and the result was sent to the client (often the whole entity representation was returned as per RESTful conventions).

This interface was being accessed by the controllers, which either only returned the queried data or invoked the requested business logic and returned the outcome. Accessing the service layer methods starts a transaction. Transactions were needed by both responsibilities --- while updating the database entities needs a transaction on its own, the queries need a Hibernate session, which can be accessed by starting a read-only transaction.

In the process of refactoring, the interface of this layer was preserved and modified only slightly to reflect changes in the refactored domain model. Changes to the implementation related mainly to the non-query methods. Transactional behavior was removed from these methods because managing the transactions in the business logic is now the responsibility of Axon's command handlers and their unit of work. The query methods as stated above remained almost unaffected by the refactoring.

The implementation of the original non-query methods was altered so they don't execute any business logic directly. Instead, the execution was delegated to the command handlers and aggregates in the new domain model. The responsibility of the methods now is to instantiate a new command with correct data (usually passed as method arguments) and send them through the command gateway to the respective command handler that processes the command. 

An important change from the original implementation is that the service methods that are responsible for creating new domain objects (aggregates) are now also responsible for creating their unique identifier (ID). In the original design, this was done by the database when persisting new entities just by incrementing the table's internal sequence number. In the new implementation, IDs are randomly generated, before the command is dispatched, using a statistically safe algorithm and then used to reference the aggregate.

To summarize, the interface of the service layer remains almost unaffected by the refactoring and is still used by controllers in the same way as in the original design. The implementation of the business logic methods, however, changed to delegation of the processing to the new domain model by dispatching commands. An example of such method is shown in Listing X**reference needed**.

    public void deleteLabel(String labelId) {
        commandGateway.sendAndWait(new DeleteLabelCommand(
                LabelId.of(labelId)
        ));
    }
