# Comparison to traditional layered architecture

This last chapter before concluding the diploma thesis compares the presented design patterns to the traditional layered architecture. The reason why this architecture figures in the comparison is not accidental. Not only is it used in Integration Portal, but also it's the most used architectural pattern in Java enterprise applications \cite{oreilly}. I've used this architecture myself for years in the university and at work.

CQRS takes the main role in the comparison since it represents the most radical changes in the design. However, event sourcing, even though it is just an extension of CQRS write model, will be in focus too as it affects other design choices of application.

Before getting into details, let's first settle what the term layered architecture means. After that, some of the problems with that architecture are presented followed by the comparison with CQRS and event sourcing.

## Layered architecture

Layered, or more commonly N-layer or layers, architecture is a pattern that helps to structure applications to logical layers, each one being at a particular level of an abstraction \cite{pattern-oriented}. The benefit of this pattern is that the low- and high-level issues are decoupled from each other, usually by some clean interface. The communication is then achieved from high-level layers to the low-level and back. By isolating components into layers, responsibilities should not be mixed together, and changes in one layer should not affect other layers.

It is the most common architecture pattern and the de facto standard for most Java enterprise applications \cite{oreilly}. As such, the pattern is carried out in one specific way in most JEE applications, although, it is still valid by the general definition above. Conceptually, there is nothing wrong with the general pattern, and it is still a good practice in software development. However, there are some downsides in the typical implementation of the pattern in JEE applications. Let's describe the specific implementation in that environment.

Applications are usually composed of some components. The aim of the layered architecture is to organize these components in horizontal layers, each layer having a specific role and responsibility. In a typical JEE application, according to \cite{javadocs}, there are three layers:

- **presentation layer** -- user interface and browser communication
- **service / business layer** -- business rules and functions
- **data access layer** -- an abstraction of database reads and writes

Presentation layer, for example, has just one responsibility --- display the information to a user. It does not need to know anything about how the data is stored. This is a responsibility of the data access layer.

On top of that, some kind of Object-Relational Mapping (ORM) is involved in the data access layer that maps the data in a relational database (rows of tables) into Java objects called entities or entity beans. These objects are then passed between the other layers to execute business logic on the data or to present the data to a client. Many implementations of the pattern map the ORM entities to other objects --- domain entities, data transfer objects (DTOs) ---  which are used in communication between two layers. This helps to define a strict interface between the layers. Figure \ref{layered1} shows how the layers are stacked and how they communicate. The strict separation by interfaces makes the layers more decoupled and less prone to changes in other layers. Also, implementation of the components can be completely replaced without the interface being changed.