
### Disadvantages

The layered architecture is the most common architecture pattern for valid reasons because mixing low-level (e.g. data persistence, web services) and high-level (e.g. presentation, business logic) makes the code error-prone, harder to navigate, maintain and test. In general, it is a good practice to follow this pattern. However, that doesn't mean it has no disadvantages. Let's describe some of the problems of this pattern in the following text.

#### Violation of single responsibility principle

Strict communication between stacked layers results in a situation where the business layer has to be accessed to only query data. This violates the single responsibility principle \cite{srp-violation}, that encourages classes to have only one responsibility, one reason to change. Responsibilities for validation and execution of user actions, as well as queries, permeate into both data access and business logic layers.

#### Leaky abstraction

We can look at layers as abstractions of implementation details. For example, presentation layer does not need to know how business logic is handled or where and how the data is stored. However, *all non-trivial abstractions, to some degree, are leaky*, as the law of leaky abstractions says \cite{leaky}. This leakage also affects the layered architecture. Often, there are many abstractions in sequence below presentation layer, e.g. business layer, data access layer, remote call, ORM, SQL. All these abstractions tend to leak in some way which affects the upper layers. This can influence many aspects of the system, such as performance and scalability.

#### Anemic model

The way how the layers are constructed encourages developers to create anemic domain model \cite{layered-anemic}. That is a model where domain entities contain little or no business logic and usually, just expose getters and setters of their fields \cite{anemic}. Although anemic entities follow single responsibility principle, as the only reason to change them is when data structure changes, the business logic is disconnected from the data. This goes against the basic principles of object-oriented design, where objects combine data and operations together to form one self-contained object that guards its invariants and state.

Although, this model is justifiable in simple applications, such as CRUD-based domain, it goes against best-practice principles of encapsulation and information hiding. The logic is separated to a business layer, possibly in a number of places making it hard to maintain the code. Domain objects cannot guarantee their business rules and invariants for themselves. 

#### Layer-to-layer delegation

In very simple, mostly CRUD-based, applications, code written in strictly layered design makes the components very thin with no added value \cite{oreilly}. Implementations usually just delegate calls from one layer to the other just to conform to the strict top-down way of processing a request. When implementing a new feature, it usually means to add unnecessary code to the project just for the sake of having layers.

But, as written above, separating the responsibilities makes the code less coupled. So these layers get more handy when changing implementations, e.g. when replacing the implementation of the data source.

#### Tight coupling

Even though, the layers (and their responsibilities) are decoupled from each other, they usually, based on the underlying implementation, are not loosely coupled \cite{oreilly}. Specifically, both presentation layer and business layer depend on the data access layer. Thus, data access logic is omnipresent in the code. This tends to make the system monolithic, which in turn can pose some potential issues with deployment, robustness, reliability, and scalability \cite{oreilly}. 

