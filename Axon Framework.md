## Supporting Framework

Before diving into the refactoring of Integration Portal it was decided to first look for frameworks that support the CQRS and ES principles and minimize their complexity for the programmers on the Java platform. The supporting framework should solve the common problems like persistence, event sourcing, integration and messaging. The framework should not make it difficult to integrate into an existing project. In case of this thesis, it should enable the refactoring of Integration Portal.

There are few frameworks that enable CQRS and ES with various features and in various state of quality. The following quality aspects were examined in each of the frameworks to determine the fitness for the refactoring.

#### Compatibility with CQRS and ES
The framework should support all the core details of CQRS and ES that very described in the previous chapter. On top of that, it should take into account implementation details such as support for database transactions, messaging using JMS (or other messaging systems), persistence, etc.

#### Integrability
The framework should be easy to incorporate into the existing code base without being counterproductive in the process. This means it should not dictate use of specific implementations but rather be open to various application designs and purposes.

#### Active development and support
A very important aspect for programmers to build trust in any project is the its activity in development, whether it be implementation of new features or fixing of bugs. Also user support and activity on forums or issue trackers is expected for the long-running projects.

#### Documentation
Development with any given framework or library is usually very slow and painful without (good) documentation. So the existence and the condition of the documentation is another quality aspect for the desired framework.

#### Popularity
Popularity of the framework should also be taken into account. Although it does not serve any purpose in development with the framework, it should be noted that popular frameworks might have better chance to stay, be maintained, and get support from the developers or other users.

### Axon Framework
**citation needed**
Axon Framework proclaims itself as a Java Framework for scalable and high-performance applications and being focused to make life easier for developers that want to create applications based on the CQRS principles. On top of that, it also supports event sourcing. It has released many versions in the past; the latest version at the time of writing this thesis was 2.4.3. It is actively developed, a new major version 3 is a work in progress. It has nice and detailed documentation and a sample project to showcase the code.

The code itself is carefully designed providing many extension points (to JMS or Disruptor)

### EventStore2
**citation needed**

### JDon Framework
**citation needed**
2 roky neaktivní