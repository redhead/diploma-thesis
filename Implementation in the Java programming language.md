# Implementation in the Java programming language

In this chapter, an implementation of CQRS and Event Sourcing in the Java programming language is  described. The base for the implementation was not written from scratch but an existing project is used instead. The project is meant to provide the base use cases and implementation that will be used in refactoring to CQRS and Event Sourcing principles.

Let's start this chapter by introducing the base project to the reader. This includes the description of the project's goals, its design and implementation details. Next, the new design and the path taken in implementing CQRS and Event Sourcing is presented. The implementation is based on a CQRS development framework for Java programming language called Axon Framework. Finally, the result is compared with the original design and some issues of the refactoring are discussed.

