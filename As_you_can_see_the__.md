As you can see, the interface is quite straightforward. There is a constructor that receives all the information to create a label (ID, name, and color). Following that, the signatures of methods that change the aggregate's state are defined. Notice that these methods are not setter methods like in Hibernate entities, but they represent various operations in business logic of the aggregate. These operations more or less copy the intents of respective commands, which will be described further in the text.

The implementation of the aggregate is influenced by the CQRS and ES principles, i.e. an event is emitted for each state change. An event can be processed by one event handler in the aggregate, which actually changes the aggregate's state (updates the values of the aggregate's fields). See the implementation in PRILOHA X **reference needed**. The class for the event that gets emitted in the `update()` method is presented in LISTING X **reference needed**.

	@Value
	public class LabelUpdatedEvent {

	    private final LabelId id;

	    private final String name;

	    private final String color;

	}

Note that the class is actually not compilable by the standard Java compiler. There are some final fields but no constructor to initialize them. But notice the `@Value` annotation. This annotation is part of Lombok library **citation needed** that aims to reduce number of repeated tasks and constructs needed by a developer to write a proper Java class. In compile time, the annotation defines which missing language constructs need to be added so the class compiles. 

The `@Value` annotation says, that the class is an immutable value class, meaning that it is only a container for data. At compile time, it generates a constructor, which initializes all the class fields, and it also adds a getter method for each field. This greatly helped in development to define a lot of classes (for commands and events) without the need to write constructors for each class and getter methods for each field. The library is also used in other places (e.g. aggregates) using other provided annotations. 

However, this comes with a cost that you need an IDE (Integrated Development Environment) with support for Lombok annotations, so the validation and code completion works. Usually this support is brought by some plugin or extension to the IDE. Successful compilation is achieved by a compiler annotation processor.

