### Domain modeling

In the introduction to CQRS, the building blocks used in domain modeling were described. Two of the building blocks, events and aggregates, play a major role in Axon Framework.

#### Events

As previously described, an event is a representation of a fact that something has changed in the system. A typical source of events is an aggregate. With every change in the aggregate, an event is raised. In Axon framework, we can use any Java object to represent an event. So we are not bound by any interface or abstract classes. We usually design events as immutable Java classes. The class names should capture the intent of an event, usually in the past tense by convention. It is highly encouraged to make the class serializable. A concrete event, represented by a class instance, contains the data describing the event. An example of an event class is presented in listing X**listing needed**.

	class ProductPriceUpdatedEvent {
		
		private final ProductId productId;

		private final Money newPrice;

		private final Money oldPrice;

		public ProductPriceUpdatedEvent(ProductId productId, Money newPrice, Money oldPrice) {
			this.productId = productId;
			this.newPrice = newPrice;
			this.oldPrice = oldPrice;
		}

		// ... getters ...

	}

The event class in the example is very similar to a command class presented previously. Although not enforced, it is good practice to make domain events immutable. So the class fields are final, and let's assume that the classes of the field types are immutable too.

In many cases, the data in a command and in the corresponding event, which is created while executing the command, are identical, including the fields. In the presented example, however, the event contains one additional field that carries the price for the product before the change. Of course, this field is not required, but in some event handlers it can be convenient to know the original value.

#### Aggregates

An entity or a group of entities, which is always kept in a consistent state, is called an aggregate. The aggregate root is the object on top of the aggregate tree that is responsible for maintaining this consistent state. Classes and primitive types can be used to design an aggregate's structure (entities and value objects). 

In Axon Framework, all aggregate roots must implement the `AggregateRoot` interface. This interface describes the basic operations needed by the aggregate repositories to store and publish the generated events. Aggregates are identified by an aggregate identifier. This can be any object, but the following rules must apply:

- It implements the `equals()` and `hashCode()` methods to ensure equality comparison with other instances.
- It implements a consistent `toString()` method.
- It is serializable.

Axon provides a number of implementations of the `AggregateRoot` interface, which can be extended by the programmer to create the desired aggregate. One of the implementation classes is the `AbstractAnnotatedAggregateRoot` class which uses annotations to simplify the aggregate code and its clarity. An example of an aggregate root that extends this abstract class is in listing #x **listing needed**.

	class Product extends AbstractAnnotatedAggregateRoot {

		@AggregateIdentifier
		private ProductId id;

		private String name;

		private Money price;

		public AbstractAnnotatedAggregateRoot(ProductId id, String name, Money price) {
			apply(new ProductCreatedEvent(id, name, price));
		}

		public void updatePrice(Money newPrice) {
			if (!price.equals(newPrice)) {
				apply(new ProductPriceUpdatedEvent(id, newPrice, price));
			}
		}

		@EventSourcingHandler
		public void handle(ProductCreatedEvent event) {
			this.id = event.getId();
			this.name = event.getName();
			this.price = event.getPrice();
		}

		@EventSourcingHandler
		public void handle(ProductPriceUpdatedEvent event) {
			this.price = event.getNewPrice();
		}

	}

This simple example shows the `Product` entity class which serves as the aggregate root. The class defines a few fields that represent the current state of the aggregate. In this case, all these fields contain value objects only. The first field, `id` of the type `ProductId`, is annotated with the `@AggregateIdentifier` annotation, which means that this field acts as the aggregate identifier for the repository in Axon. The `ProductId` type should follow all the rules listed above in order for Axon to work properly.

Following the fields, there is the class constructor and the method `updatePrice()`. Both of which represent the public interface to access and change the aggregate state. In most cases these are called from a command handler. The constructor creates a new aggregate instance, and the method updates the product price of an instance. Notice that their code does not change the state directly but it applies new events instead. This follows CQRS and ES principles as events represent the state transition.

Whenever a new event is applied to an aggregate, an event handler for that event type is called in the aggregate. There are two event handlers in the example that handle the respective events applied by the methods above. An event handler is the place that actually changes the state of the aggregate, i.e. updates the fields or state of other entities in the aggregate.

To make it clear, why event handlers handle the actual state change instead of the methods that emit events, take Event Sourcing into consideration. When the aggregate is being loaded by the repository, instead of retrieving the current state of the aggregate, the event sourcing repository gets only a stream of historic events instead. These events are then applied to a brand new aggregate instance one by one, causing the event handlers to execute. So, the event handlers are responsible for getting the aggregate to the desired final state. This means that the public methods are never executed when replaying the past events from the repository. This is desired by design, because otherwise it would mean that new events could be emitted while processing historic events, which would result in changing the history of the aggregate.









