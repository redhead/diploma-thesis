# Conclusion

This diploma thesis aimed at implementing the Event Sourcing and CQRS design patterns in the Java programming language. Because these patterns are not widely known in the Java world, they were briefly introduced to the reader together with Domain-Driven Design methodology. These design patterns were studied thoroughly, so they could be applied to a real Java project --- Integration Portal. The refactoring of the system's original design to CQRS and ES was achieved by using Axon Framework, a Java library that supports software development by the principles of the two patterns. The strategy and the process of the refactoring were described, followed by solutions to some problems that were encountered. Finally, the original layered architecture was described pointing out its disadvantages, and compared to the new design. To conclude, the following paragraphs evaluate the outcome of the thesis, particularly the use of the two design patterns in Java and in general.

## Evaluation of CQRS and Event Sourcing

CQRS and Event Sourcing are definitely interesting patterns to further explore. Not only they are empowering and enable a lot of possibilities to developers, but they even improve the overall design and architecture of an application. It reassured me of what I don't like about the traditional layered architecture in enterprise applications.

A nice outcome of the refactoring was that the patterns didn't bring a lot of breaking changes to the project. Although, the application of the patterns usually results in bringing message queues, buses, and asynchronous execution, which means a lot of work around the structure and deployment of the application, this was not the case in the refactoring. The application remained a monolithic, single module which made the refactoring be a gradual improvement. But if the time comes for the application to increase the scalability and availability, the CQRS with Axon Framework enables that very easily by changing configuration (and a bit of the code).

However, both of the patterns are not very easy to grasp. The steep learning curve is the major disadvantage of the patterns and DDD, which makes the adoption of the patterns in businesses difficult. There is a lot to process for the developers and makes the structure of the architecture more complex --- self-contained modules, asynchronous execution, queues, etc. Also, they require that developers change their mindset built by the traditional applications --- transaction scripts, immediate availability and synchronous execution.

Overall, the patterns should get more of the popularity because they enable a lot of possibilities (some described in \ref{event-sourcing-and-cqrs-design-patterns} \nameref{event-sourcing-and-cqrs-design-patterns}) and lead to better domain-oriented, scalable and available applications. The path to that is not easy, but it is rewarding at the end. Moreover, the clean and hexagonal architectures, which are more convincing than the traditional layered architecture, can be built on top of CQRS.

## Implementation in Java

In Java, the use of Axon Framework to implement the patterns was a good decision, although there are some issues regarding implementation details that the framework enforces to the code being developed. Some of the downsides should be resolved in the next major version, which should be released soon \cite{axon}. Even though the framework does not use the now popular actor model and reactive programming that fit with the paradigms, it is a solid, well-tested, integrable piece of a library with many great features that makes the implementation of CQRS and ES straightforward in Java.

On the other hand, Java is not a convenient language to implement CQRS and ES, because the language is still very conservative about features that could be beneficial in the development. Other languages, e.g. C# or Scala, have a better support of making the code more maintainable and easy to read. For Java, I decided to use Lombok library to diminish some of the inconveniences.

## Integration Portal

I would recommend using the refactored version of Integration Portal because it enables more flexibility than the original design. The command- and event-based system definitely fits the domain and can be leveraged in the integration of other systems. Querying the REST interface could also be well optimized by using specialized read models stored, for example, in a document database like MongoDB rather than the rational database.

However, the biggest downside is that the Integration Portal team would need to know these patterns well enough to expand the code. And as stated previously, the learning curve is pretty steep and takes a lot of time to learn CQRS and Event Sourcing (as well as the Domain-Driven Design concepts), not to mention Axon framework. For these reasons, I would recommend to continue on the refactored version to interested people only. On the contrary, expanding the functionality of the original design could backfire in the long term because of the mixed concerns and code complexity, which will definitely rise if not taken care of.

## Final thoughts

Overall, I am satisfied with the outcome of this project. I expanded my knowledge of design patterns that can help me make better applications. My personal experience with the CQRS and Event Sourcing patterns was very educational. Although, I may not use the patterns in every future application, they certainly changed my mindset about the architecture and structure of applications and that it is important to reason about it in details.

The implementation of the patterns resulted in better and cleaner architecture of the Integration Portal project, and also enabled new possibilities to expand its functionality and to easily add new features. However, there was a lot to learn to make it happen and there was not enough time to incorporate the expanded CQRS design with asynchronous execution and a number of optimized read models. This idea is left for future work. 

In conclusion, I found the patterns to be very interesting, and I will definitely explore them more in the future.