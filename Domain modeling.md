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



