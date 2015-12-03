
### Command handling

As previously described, a state change in CQRS starts with a command. To represent a command, that can handled by Axon, an instance of a Java class is used. Various types of commands are represented by different classes, e.g. `AddProductToCartCommand` or `UpdateProductPriceCommand`. The class name captures the intent of the command and the fields in the class instance carry the command data. For example, the `UpdateProductPriceCommand` class could have fields for the new price of the product and for the unique identification number (ID) of the product that the command targets. In Java, the code of that class could look like in listing #X **listing needed**.

	class UpdateProductPriceCommand {
		
		private final ProductId productId;

		private final Money newPrice;

		public UpdateProductPriceCommand(ProductId productId, Money newPrice) {
			this.productId = productId;
			this.newPrice = newPrice;
		}

		// ... getters ...

	}

The command name, represented by the class name, captures the intent of the command. It uses the imperative naming convention, in comparison to the naming convention of events, which use the past tense instead.

The next paragraphs provide an overview of the components related to setting up the infrastructure for command handling in Axon.

#### The Command Gateway

To dispatch the commands represented by class instances, Axon provides a convenient interface called a Command Gateway. This interface defines two methods to send a command, which is passed as an argument. The `sendAndWait()` method is blocking, which means it stops the execution of the caller until the passed-in command is resolved, and it returns the result of the command execution. The other method, `send()`, is the asynchronous counterpart, i.e. non-blocking the execution of the caller. Axon provides default implementation of this interface called `DefaultCommandGateway`, or the users can provide their own implementation to suit their needs. The commands passed to the Command Gateway are then send to the Command Bus. 

#### The Command Bus

An entry point to the Axon's command dispatching mechanism is the Command Bus. Its responsibility is to route the received commands to the command handlers. Each command is always sent to the exactly one command handler. The interface defines two methods for dispatching the commands, which are designed with asynchronicity in mind. It also provides methods for (un)subscribing the command handlers. 

The `SimpleCommandBus` is the simplest implementation of the `CommandBus` interface. It provides a way of intercepting the command object before it is actually dispatched to the command handler. To intercept the command, in order to modify, validate, or block the command, the command bus needs to be configured with a `CommandDispatchInterceptor`. Additionally, a Unit of Work, which will be described further, is maintained for each sent command by the bus.

There are other implementations of the `CommandBus` interface provided by Axon. One of them uses Disruptor **reference needed** which takes different approach to multithreaded processing to increase performance. Another implementation provides distribution of command buses and command handlers across different JVMs (Java Virtual Machines).

#### Command Handlers

In Axon, a Command Handler is an object that receives a command of specific type and takes action based on its contents. There are a few ways to create a command handler. One is based on implementing the `CommandHandler` interface and its `handle()` method to process the given command. The command handler is than subscribed to the command bus by specifying the type of the command it handles. 

Sometimes it is beneficial to have multiple closely related command handlers in one object. This approach can be achieved by using the `@CommandHandler` annotation on methods to mark them as command handlers. The type of the command that the method can handle is determined by the type of the first parameter. The object can be a simple POJO (plain old Java object) and only the method annotations determine that they are command handlers. 

The support for Spring framework enables automatic subscription of the command handlers to the command bus when the object is turned into to a Spring bean. We can use the Spring's inversion of control container to inject dependencies of command handlers.

The dependencies for the command handlers are usually the aggregate repositories, which handle loading of aggregates and persisting their changes. As stated in the introduction to CQRS, command handlers are supposed to load an aggregate instance, call a method on it, and save the aggregate changes.

#### Unit of Work

The processing of a command can be seen a single unit. Each time the command handler executes, it is tracked in a Unit of Work. When command handling is finished, the current Unit of Work is committed and all actions are finalized. This means that aggregate repositories are notified about the changes in aggregates and saves them. In case of event sourced aggregates, the new events applied to the aggregates are stored to an event log and published to an Event Bus.

Note that the Unit of Work is not a replacement for transactions. However, the Unit of Work can be bound with a transaction by configuring the transaction manager. This transaction will be started at the beginning of the command handler execution and committed when finished. If any exception is thrown, the bound transaction are rolled back.




