# Implementation in the Java programming language

In this chapter, an implementation of CQRS and Event Sourcing in the Java programming language is  described. The base for the implementation was not written from scratch but an existing project is used instead. The project is meant to provide the base use cases and implementation that will be used in refactoring to CQRS and Event Sourcing principles.

Let's start this chapter by introducing the base project to the reader. This includes the description of the project's goals, its design and implementation details. Next, the new design and the path taken in implementing CQRS and Event Sourcing is presented. The implementation is based on a CQRS development framework for Java programming language called Axon Framework. Finally, the result is compared with the original design and some issues of the refactoring are discussed.

## Introduction to Integration Portal

The project, known under the working title Integration Portal, aims to integrate several systems of the Czech Technical University (CTU) for the purpose of file sharing and archiving. It is a web application where users (members of CTU and externs) can upload, share and organize files in a way that resembles a regular computer file system. The file data is stored in a data storage infrastructure provided by the CESNET association. 

Please note that the project is still a work in progress and some of the functionality and requirements described below are in preparation or active development at the time of writing this thesis. The refactoring to CQRS and event sourcing described further in this chapter deals only with the currently working functionality at that time.

The system is divided into two parts. The first part is the front-end user interface presented to users in their web browser. The second part is the back-end server that integrates the systems and provides a communication interface for the front end.


### The front end

The front-end presentation layer of the project is an client-side HTML5/JavaScript application written using AngularJS framework **citation needed**. It is a so-called single-page application, that means the browser loads the web page only once and all the other communication is done using asynchronous calls to the server in the background. The page refreshes the content for the user by dynamic modification of the page using JavaScript.

The front-end part presents the users with the files they uploaded or they have access to through the sharing functionality. It provides means of interaction with the files, i.e. moving, renaming, deleting, organizing to folders, etc. It also includes user interface for logging into the system with user's credentials and a section for administrators. 

However, all the actual processing and state transitioning is done on the back-end part and the front end is only used for displaying the content and sending the commands to the back end. The communication is done using a REST API (Application Programming Interface).

Because the front end is not really a subject of refactoring to CQRS and event sourcing, it won't be discussed in any more detail. For more information see **citation needed**


### The back end

The back-end server application uses a traditional three-tier architecture in Java Enterprise Edition implemented using Spring Framework and its supporting libraries for web development, security, database access, and REST API services. A PostreSQL database is used to persist the application data.

One of the objectives of the server-side application is to integrate the systems to accomplish the goal of file system management for CTU members. The systems are described below.

#### FELid

A global authentication and authorization system of CTU academics and students supporting single sign-on functionality. This system will be used to authenticate the users of Integration Portal.

#### KOSapi

The system that enables applications to access various types of information, e.g. student's details or subject schedules, through a RESTful web service. This system will be used to retrieve a list of organizational units of the faculty that map to faculty departments. Each portal user belongs to one organizational unit. Organizational units are assigned with a quota on amount of space that is available for users' files.

#### CESNET 

An association of universities of the Czech Republic and the Czech Academy of Sciences that operates and develops the national e-infrastructure for science, research, and education. Integration Portal uses CESNET's data storage to store file data. It provides a communication interface to upload and download files and it also supports two different types of media that the files can be stored to, on-line and off-line. They types differ in speed and capacity. Rarely used files are automatically move off-line to slow but high-capacity magnetic tapes (manual switch is also possible). In case the file is needed again, it can be moved on-line seamlessly.

### Architecture


## Axon Framework

### Aggregates

### Comamnds

### Events

### Sagas


## Refactoring of the Integration Portal

### REST interface

### Testing


## Summary
    ???