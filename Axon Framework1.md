## Axon Framework

In this section, let's introduce the reader to Axon Framework's specifics that relate to the refactoring to CQRS and ES further in text. For more details, see the framework's documentation **citation needed**.

### Spring Framework support

As mentioned previously, Axon Framework very well supports Spring Framework which is also used in Integration Portal. It is not required to use Spring Framework in order to use Axon Framework, however, it greatly simplifies the configuration if Spring is used. Axon provides namespace support for Spring XML configuration of the Application Context to set up the Axon's building blocks.

Axon Framework doesn't hide the CQRS details from its users and so it shares the CQRS terminology. The building blocks described in CHAPTER x.y.z **reference needed** are used to build the domain model with Axon Framework. On top of these building blocks, some infrastructure and configuration is needed to set up Axon. The following paragraphs describe the concepts that are specific to Axon Framework implementation.

### Command handling

As previously described, a state change in CQRS starts with a command. To represent a command, that can handled by Axon, an instance of a Java class is used. Various types of commands are represented by different classes, e.g. `AddProductToCartCommand` or `UpdateProductPriceCommand`. The class name captures the intent of the command and the fields in the class instance carry the command data. For example, the `UpdateProductPriceCommand` class could have fields for the new price of the product and for the unique identification number (ID) of the product that the command targets. In Java, the code of that class could look like in listing #X **listing needed**.

	class UpdateProductPriceCommand {
		
		private final ProductId productId;

		private final Money newPrice;

		// ... getters ...

	}

The command name, represented by the class name, captures the intent of the command. It uses the imperative naming convention, in comparison to the naming convention of events, which use the past tense instead.

The next paragraphs provide an overview of the components related to setting up the infrastructure for command handling in Axon.









