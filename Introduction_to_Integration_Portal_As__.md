## Introduction to Integration Portal

As stated in the introduction to this chapter, the CQRS and ES patterns were examined on an existing project. This section introduces the project that was chosen for refactoring into the CQRS and ES principles.

The project, known under the working title Integration Portal, aims to integrate several systems of the Czech Technical University (CTU) for the purpose of file sharing and archiving. It is a web application where users (members of CTU and externs) can upload, share and organize files in a way that resembles a regular computer file system. The file data is stored in a data storage infrastructure provided by the CESNET association. 

Please note that the project is still a work in progress and some of the functionality and requirements described below are in preparation or active development at the time of writing this thesis. The refactoring to CQRS and event sourcing described further in this chapter deals only with the working functionality at that time.

The system is divided into two parts. The first part is the front-end user interface presented to users in their web browser. The second part is the back-end server that integrates the systems and provides a communication interface for the frontend.


### The frontend

The front-end presentation layer of the project is a client-side HTML5/JavaScript application written using AngularJS framework **citation needed**. It is a so-called single-page application, that means the browser loads the web page only once and all the other communication is done using asynchronous calls to the server in the background. The page refreshes the content for the user by dynamic modification of the page using JavaScript.

The front-end part presents the users with the files they uploaded or they have access to through the sharing functionality. It provides means of interaction with the files, i.e. moving, renaming, deleting, organizing to folders, etc. It also includes a user interface for logging into the system with user's credentials and a section for administrators. 

However, all the actual processing and state transitioning is done on the back-end part and the frontend is only used for displaying the content and sending the commands to the backend. The communication is done using a REST API (Application Programming Interface).

Because the frontend is not really a subject of refactoring to CQRS and event sourcing, it won't be discussed in any more detail. For more information see **citation needed**


### The backend

The back-end server application uses a traditional three-layer architecture in Java Enterprise Edition implemented using Spring Framework and its supporting libraries for web development, security, database access, and REST services. A PostreSQL database is used to persist the application data.

One of the objectives of the server-side application is to integrate the systems to accomplish the goal of file system management for CTU members and externs. The systems are described below.

#### FELid

A global authentication and authorization system of CTU academics and students supporting single sign-on functionality. This system will be used to authenticate the users of Integration Portal.

#### KOSapi

The system that enables applications to access various types of information, e.g. student's details or subject schedules, through a RESTful web service. This system will be used to retrieve a list of organizational units of the faculty that map to faculty departments. Each portal user belongs to one organizational unit. Organizational units are assigned a quota on the amount of space that is available for users' files.

#### CESNET 

An association of universities of the Czech Republic and the Czech Academy of Sciences that operates and develops the national e-infrastructure for science, research, and education. Integration Portal uses CESNET's data storage to store file data. It provides a communication interface to upload and download files and it also supports two different types of media that the files can be stored to, on-line and off-line. Their types differ in speed and capacity. Rarely used files are automatically moved off-line to slow but high-capacity magnetic tapes (a manual switch is also possible). In case the file is needed again, it can be moved on-line seamlessly.

### Architecture

The architecture follows a typical setup for Java Enterprise Edition applications called a three-tier architecture, where the client-side application written in AngularJS being the presentation tier, the back-end server being the application tier and the PostreSQL database server being the data tier. On top of that, Integration Portal communicates with other external systems through various channels to accomplish the integration goal. As this thesis focuses on refactoring the back-end server to CQRS and Event Sourcing, let's describe its implementation in more detail. 

The internal code of the back-end application is organized into logical layers, an arrangement which is a widely adopted best practice in Java EE application development under the name layered architecture (or N-layer architecture). Each layer is responsible for some functionality of the application without being mixed with the rest. The description of the three layers found in the back-end application and their responsibility is following.

#### The controller layer
The entry layer for the back-end application is formed by a number of controllers which handle the requests to the server. In Integration Portal, a REST API implemented using HTTP protocol is used to communicate with the front-end application. So most of the controllers are responsible for validating the HTTP requests, parsing the data sent with them and passing the flow to the next layer. Also, the controllers construct responses including the data from this layer and send them back to the UI application. Additionally, in case of any error in upper layers, the controllers are responsible for sending correct status codes in the HTTP response. These entry points to the back-end application are also protected by user authorization mechanism, so not every user is allowed to call any controller handler method. The controllers are implemented using Spring Framework MVC support and the authentication and authorization by Spring Security framework.

#### The service layer
The service layer represents the interface through which the business logic is invoked. It consists of services represented by classes that implement operations in the business domain. The services are used by the controllers to invoke the requested operation in the service layer. The responsibility of this layer is to maintain the data consistency for each operation and also controls the data modification for the data access layer and integration with external systems.

#### The data access layer
The last layer is responsible for the data access and storage. Most of the data is stored in the PostgreSQL relational database. This layer mainly consists of classes called Data Access Objects (DAO) that access the data stored in the database through Object-Relational Mapping (ORM) framework called Hibernate. The data is retrieved from the database using Hibernate and SQL, and mapped to instances of appropriate Java classes called entities. The entities are then used by the service layer to modify the data to fulfill the business requirements. Finally, through Hibernate, this layer saves the modifications again into the database.

