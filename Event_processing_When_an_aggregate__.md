### Event processing

When an aggregate instance is successfully saved, whether by storing new domain events or by storing the whole aggregate state, the generated events need to be dispatched to other components, e.g. query database, search engines, external systems, etc. These components are called event listeners and the dispatching is done by the Event Bus.

#### Event Bus

The mechanism that dispatches events to the subscribed event listeners is called the Event Bus. Axon provides two implementations, `SimpleEventBus` and `ClusteringEventBus`. Both implementations manage the subscription of event listeners and forward all incoming events to respective listeners.

The `SimpleEventBus` is suitable for dispatching events synchronously and locally on one JVM (Java Virtual Machine). It sequentially passes incoming events to all the registered event listeners. The `ClusteredEventBus` allows grouping of event listeners to so-called clusters based on their properties and non-functional requirements. It also supports dispatching events to different JVMs on different machines.

#### Event Listeners

Event listeners are components that react to the incoming events. All event listeners must implement the `EventListener` interface, which describes a single method that receives the event as a parameter, the `handle()` method. Implementing classes need to register with an event bus, so they can receive the events published to the bus.

The `handle()` method sequentially receives all the events published to the event bus. However, the implementing classes very rarely need to listen to all the types of events. In order to pick just the events the listener wants to process, the method becomes cluttered with many if-else or switch statements. Fortunately, Axon provides annotation based event listeners where multiple methods can handle different events based on the type of the event (effectively, based on the event class). The handler methods need to be annotated with the `@EventHandler` annotation. In that case, the listeners can be any objects not requiring to implement the interface. The object is then wrapped by the `AnnotationEventListenerAdapter` class which translates the communication between the interface and the annotated methods. See listing X **listing needed** for an example.
	
	class ProductEventListener {

		@EventHandler
		public void handle(ProductCreatedEvent event) {
			// code to handle the event
		}

		@EventHandler
		public void handle(ProductPriceUpdatedEvent event) {
			// code to handle the event
		}

	}

On top of that, the Axon's Spring support makes it easy to integrate the listeners with the Spring's IoC container. This support takes care of automatically wrapping the listener in the adapter class and registering it with the event bus. It also enables injecting bean dependencies into the listener, so they can be used by the handler methods.






