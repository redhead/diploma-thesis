### Disadvantages

As with every existing design choices, CQRS and Event Sourcing has definitely some disadvantages too. In the following text, the description of those that occurred in the refactoring are presented.

#### Steep learning curve

The CQRS design pattern with Event Sourcing implemented using Domain-Driven Design methodology and terminology is a lot to process for a developer. It takes a lot of practice and experience to know what is right and what is wrong for the application. For newcomers, it is very hard to grasp all the essential aspects of the design, and more importantly to use it well. In businesses, this steep learning curve can play a major role in deciding whether to use this design or go the traditional way. 

#### Code explosion

In the original design, there were only interfaces that defined methods of the service layer, and then the implementation that used entities to do business logic and querying. Using Axon's way of implementing CQRS, there was considerably more code to create and maintain. For every command method before, there is now a new command class. For each original entity, there is now an aggregate that the commands target. And for each state change in the aggregates, there is an event class. 

All these classes created in the refactoring are contained in close to 120 new files! This adds more complexity to the structure of Integration Portal and makes it harder for developers to orient themselves in the project.


#### Code navigation

Axon Framework implements CQRS using the standard Java language constructs. That means, that all the features your IDE provides for development in Java, works here as well. Most of the popular IDEs know how to navigate their users to declarations and code usages, e.g. methods, fields or classes. However, the way how code is decoupled by Axon Framework, makes it significantly harder to use these features. The problem is caused by by the nature of commands and events being passed around without specifying direct connection to methods for their publishing or handling. For example, to find a handler method for a command or event, developers need to search for an annotated method with an argument of the same type as the command or event. This way is not very convenient, nor fast for the developers.

The authors of Axon Framework knew about this annoyance, so they've created a plugin for IntelliJ IDEA, a Java IDE. The plugin makes navigation to and from handler methods easier. Sometimes, however, it is not fully reliable and stops working, from my experience.