## Supporting Framework

Before diving into the refactoring of Integration Portal, it was decided to first look for frameworks that support the CQRS and ES principles and minimize their complexity for the programmers on the Java platform. The supporting framework should solve the common problems like persistence, event sourcing, integration and messaging. The framework should not make it difficult to integrate into an existing project. In the case of this thesis, it should enable the refactoring of Integration Portal.

There are few frameworks that enable CQRS and ES with various features and in various state of quality. The following quality aspects were examined in each of the frameworks to determine the fitness for the refactoring.

#### Compatibility with CQRS and ES
The framework should support all the core details of CQRS and ES that very described in the previous chapter. On top of that, it should take into account implementation details such as support for database transactions, messaging using JMS (or other messaging systems), persistence, etc.

#### Integrability
The framework should be easy to incorporate into the existing code base without being counterproductive in the process. This means it should not dictate the use of specific implementations but rather be open to various application designs and purposes.

#### Active development and support
A very important aspect for programmers to build trust in any project is its activity in development, whether it be an implementation of new features or fixing of bugs. Also, user support and activity on forums or issue trackers is expected for the long-running projects.

#### Documentation
Development with any given framework or library is usually very slow and painful without (good) documentation. So the existence and the condition of the documentation is another quality aspect for the desired framework.

#### Popularity
The popularity of the framework should also be taken into account. Although it does not serve any purpose in development with the framework, it should be noted that popular frameworks might have better chance to stay, be maintained, and get support from the developers or other users.

### Axon Framework
**citation needed**
Axon Framework proclaims itself as a Java Framework for scalable and high-performance applications and being focused on making life easier for developers that want to create applications based on the CQRS principles and concepts, i.e. commands, events, aggregates, entities, sagas, etc. On top of that, it also supports event sourcing. 

It has released many versions in the past; the latest version at the time of writing this thesis was 2.4.3. It is actively developed, a new major version 3 is a work in progress. It has nice and detailed documentation and a sample project to showcase the code. It aims to ease the development of the domain model and business logic and shields the programmers from the implementation traps of CQRS and ES.

The code of Axon Framework is carefully designed providing a few extension points (e.g. to JMS or Disruptor). It supports the Spring Framework's configuration and dependency injection. It also uses Java annotations for building the domain model and event listeners without being tied to the Axon's specific logic. However, it does not try to hide CQRS architecture details or its components, so it is better to have the knowledge of CQRS.

The code repository on GitHub has more than 300 stars and 100 forks. **citation needed** The user community is active on the mailing lists and on the issue tracker. The code is open source under the Apache 2 license. The authors also provide commercial services such as support contracts, training, and consultancy.

### EventStore2
**citation needed**
EventStore2 is a framework for creating event sourced applications in Java. Much like Axon Framework it uses CQRS concepts and terms for the implementation, and it utilizes the Java annotations to configure command handlers, sagas and event handlers (called Projections here). For asynchronous execution and clustering support it uses Akka, a toolkit and runtime for building highly concurrent, distributed, and resilient message-driven applications on the JVM.

It is in active development, with the current stable version being 2.4.3. A big disadvantage is lack of detailed documentation. The whole framework is described just in few paragraphs in a README file with few examples of how to implement command handlers and projections. The documentation totally omits how to use sagas, transactional support, persistence, configuration and more.

The code base is hosted in a GitHub repository. It has 24 stars and 2 forks. The issue tracker does not contain any issues, not even closed. No official web site or any other references where found on the Internet. From this, it was deduced that the framework is not actively used by many users. It is open source under the MIT license.

### Jdon Framework
**citation needed**
The Jdon Framework web site describes the framework as a Java reactive framework that can be used to build Domain Driven Design applications using CQRS and event sourcing with asynchronous concurrency and higher throughput. It uses reactive approach and actor based model, similarly to EventStore2 which uses Akka. It supports dependency injection and annotation support and is integrable with Spring framework.

The documentation is comprehensive and uses images to better explain the principles. However, typographically it is in a poor state for reading - often the text is broken, it contains typos and grammar errors. On top of that, the framework does not seem to be developed anymore. The last change made to its code was two years ago. The latest stable version 6.8 was released around that time too.

The framework's code is stored in a GitHub repository with 278 stars and 148 forks. A few issues are in the issue tracker, some written in Chinese. The latest issues are asking about the activity of development, but without any responses. It is open source under Apache 2 license.


### Summary and framework selection

Since CQRS and event sourced applications are not very common in the Java world, not many frameworks exist. In the search for these frameworks on the Internet, I focused on projects that aim to be used by many users (not a sample or "playground" project of one author) with good documentation and active development. The three frameworks I found that were worth to study are described above.

EventStore2 and Jdon both use actor model **reference** and reactive approach for concurrent execution, where Akka seems to dominate the Java (and Scala) world in that matter nowadays. As I would very much like to get into reactive programming in Akka, the two frameworks were found unconvincing to be chosen for the refactoring. 

EventStore2 is actively developed but does not provide any other support (as in the documentation, forums, mailing lists or web site) and it also lacks user base. On the contrary, Jdon Framework didn't lack these things but it seems to have completely stopped the development without any message to the users, and there are even no responses to new issues.

Axon Framework, on the other hand, excels in almost all the quality aspects that were inspected. The downside is that it doesn't use actor model or reactive programming. It is more conservative which can be advantageous in some cases. Thus, Axon Framework was chosen to support the refactoring of Integration Portal to CQRS and Event Sourcing.