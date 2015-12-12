### Command processing

To send the commands that invoke business logic on an aggregate, the service layer was modified as previously explained. Expanding on the example of the label functionality, let's describe the transformation of the `LabelService` implementation regarding a creation of a label. Originally, the responsibility of the service implementation was to retrieve the `Label` entity (or create a new instance), do some business logic that modified the entity, and finally save the result to the database. After refactoring, the business logic method turned into factories for commands that were send to the Axon's command gateway to be dispatched. Take for example, the `createLabel()` method presented in Listing X**reference needed**. After refactoring, it only delegates the execution of business logic using a new command instance that is sent to the command gateway.

    public void createLabel(String name, String color) {
        String id = UUID.randomUUID().toString();

        commandGateway.sendAndWait(new CreateLabelCommand(
                LabelId.of(id), name, color
        ));
    }

As you can see, apart from sending a command, it also creates an identifier for the new aggregate instance being created by the command. This is a change from the original design, where ID was created by the read/write database on insert. As the read model is now a side effect of processing events, the identifier needs to be created beforehand. This also makes scaling and asynchronous command processing easier, because the ID is known before any read model is updated, and can be returned to the client immediately.

@Value
@EqualsAndHashCode(callSuper = true)
public class CreateLabelCommand extends UserAwareCommand {

    private final LabelId id;

    private final String name;

    private final String color;

}

The dispatched command, defined by the class in Listing X**reference needed**, is forwarded to the command bus (`SimpleCommandBus`) which executes the respective command handler. But just before that, it is intercepted by `AddUserCommandDispatchInterceptor`. As you can see, the command class extends `UserAwareCommand`, which is an abstract class that adds the `sentBy` field. This field is intended for holding the identifier of the user that issued the command. To reduce code duplication of setting the identifier of the currently logged-in user to every command, the interceptor is used to set the user identifier just before it is dispatched by the command bus. Also notice that the command class is also declared with the `@Value` and `@EqualsAndHashCode` annotations from Lombok library.

Most of the time, command handlers follow the same pattern---load the aggregate from the repository, call a method on the aggregate, and finally save the aggregate back to the repository. The actual storing mechanism depends on the repository type (e.g. event sourcing repository). However, in case of commands that create aggregate instances, the command handler must instantiate the aggregate and add it to the repository. The implementation of the command handler for `CreateLabelCommand` is presented in Listing X **reference needed**.

    @CommandHandler
    public void createLabel(CreateLabelCommand command) {
        checkUnique(command.getName(), command.getColor(), command.getSentBy());

        Label label = new Label(
                command.getId(),
                command.getName(),
                command.getColor(),
                command.getSentBy()
        );

        repository.add(label);
    }

A command must be valid so it can be processed by a command handler. In this case, the command handler checks that a label with the given attributes does not exist yet (as label's name and color are unique per user). Then it creates a new `Label` aggregate by calling the constructor. The constructor applies the `LabelCreatedEvent` in the process. Finally, the aggregate is added to the repository. As the repository for Labels uses event sourcing, it saves the applied event to the event store.

