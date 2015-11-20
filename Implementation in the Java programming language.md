# Implementation in the Java programming language

In this chapter, an implementation of CQRS and Event Sourcing in the Java programming language is  described. The base for the implementation was not written from scratch but an existing project is used instead. The project is meant to provide the base use cases and implementation that will be used in refactoring to CQRS and Event Sourcing principles.

Let's start this chapter by introducing the base project to the reader. This includes the description of the project's goals, its design and implementation details. Next, the new design and the path taken in implementing CQRS and Event Sourcing is presented. The implementation is based on a CQRS development framework for Java programming language called Axon Framework. Finally, the result is compared with the original design and some issues of the refactoring are discussed.

## Introduction to Integration Portal

The project, known under the working title Integration Portal, aims to integrate several systems of the Czech Technical University (CTU) for the purpose of file sharing and archiving. It is a web application where users (members of CTU and externs) can upload, share and organize files in a way that resembles a regular computer file system. The file data is stored in a data storage infrastructure provided by the CESNET association.

The system is divided into two parts. The first part is the front-end user interface presented to users in their web browser. The second part is the back-end server that integrates the systems and provides a communication interface for the front end.


### The front end

The front-end presentation layer of the project is an client-side HTML5/JavaScript application written using AngularJS framework **citation needed**. It is a so-called single-page application, that means the browser loads the web page only once and all the other communication is done using asynchronous calls to the server in the background. The page refreshes the content for the user by dynamic modification of the page using JavaScript.

The front-end part presents the users with the files they uploaded or they have access to through the sharing functionality. It provides means of interaction with the files, i.e. moving, renaming, deleting, organizing to folders, etc. It also includes user interface for logging into the system with user's credentials and a section for administrators. 

However, all the actual processing and state transitioning is done on the back-end part and the front end is only used for displaying the content and sending the commands to the back end. The communication is done using a REST API (Application Programming Interface).


### The back end



#### CESNET 
Provides a communication interface to transfer the files content.
This infrastructure also supports two different types of media that the files can be stored to. The types differ in speed and capacity.

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