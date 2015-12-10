# Refactoring of Integration Portal

Now that all the principles, concepts, and tools were settled and explained, the whole process of refactoring the Integration Portal application to the CQRS/ES design is presented. This section includes describing the preparation and strategy chosen for the task, the sole evolution of the refactoring, and propositions of solutions to some problems that occurred in the process. In conclusion, a few ideas for the future extensions of this design to implement new features is suggested.

## Preparation

In the preparation to the refactoring, it was needed to go through the whole back-end code to get knowledge of all the features and functionality that would be subjected to the refactoring. In the process it was found out that some of the code needs to be revised to better fit the CQRS design.

The first thing was to to untangle the dependencies of the components and layers to find out how they are connected and how it affects the refactoring. In the original design, where CQRS and ES principles were not taken into account, some of the code aspects were believed to cause trouble in the refactoring. Because of that, the code underwent some changes before the refactoring.

### Revising the REST interface

After reading CQRS JOURNEY**citation needed**, the idea of task-based user interface, described in (TASK BASED UI)**reference needed**, was taken into consideration in regards to the Integration Portal UI. A RESTful application programming interface mediates the communication between the front-end user interface and the back-end logic. 

The API was designed according to the typical RESTful concepts. Every entity in the back end (User, Folder, File, etc.) was represented as one resource in the interface. The four basic HTTP methods - GET, POST, PUT, and DELETE - were defined on most of the resources in order for the UI to take actions on the entities in the back end. These methods effectively convert to the known CRUD scheme (Create, Read, Update, Delete). The create, read, and delete actions are usually straightforward using the POST, GET, DELETE methods respectively. A great care must be taken regarding the update action. There are often multiple features on a resource that can be updated. With CRUD-based REST interface, it is possible to use one HTTP method (PUT) to modify multiple different components of one resource in a single request (e.g. change a folder name and set new label).

The design of general update command in RESTful API can be problematic when using CQRS, because the intent of the request becomes unclear. From the perspective of the user interface (UI), there is no real business connection between changing a folder name and setting a new label at the same time. Even the UI never sends one PUT request to alter multiple unrelated features of one resource. So a general-purpose update request on a resource is exaggerated abstraction that hides the intent of the change. The importance of capturing intents in commands and events is explained in the CHAPTER ABOUT CQRS **reference needed**.

To solve this problem, it was decided to redesign the back-end REST API so the update request carries the intent clearly with one component to change at a time. A few ways how to carry out the redesign were considered. They are described in detail further in ?REST REDESIGN?**reference needed**. For now, let's just describe the chosen way of the new arrangement of the REST interface.

First, all the modifiable features of each resource were collected. Each feature was assigned a sub-resource of the original resource URL. To carry the intent of the change, a POST request is sent on this newly created URL for each feature. Consider the following example of REST API interface to change a name of a folder resource:

Before the redesign

    PUT /folder/abc123
    {"name": "New folder name"}
    
After the redesign

    POST /folder/abc123/nameChange
    {"name": "New folder name"}

As you can see, the difference is made by a shift in the URL to target one specific feature of the original resource. This way, changing multiple features in one request is not possible. The `nameChange` sub-resource expects modification of the name only and thus carries the intent clearly - changes the name. The rest of the modifiable features of each resource was redesigned in a similar way.

### Revising the original domain model

Concurrently, development of a new feature was considered to extend the functionality in the original code. The functionality should have allowed users to share their files with other users, which was not possible before. This also included the ability to specify permissions on the shared files for each user (or group). The responsibility of the development was assigned to another member of the Integration Portal team. The time of completion of this functionality was expected to be during the refactoring and thus it was originally intended to be part of the refactoring as well.

This resulted in some changes to the original domain model to support this functionality. Specifically, there was a small issue in the design of the entities in the original domain model that prevented sharing the same code for both folders and files. This issue was even more problematic for development of the new functionality. 

Both folders and files represent nodes in the simulated file system tree. Conceptually, they are very similar entities. They even share some of their fields, like the name, parent folder, owner, assigned labels, etc. However, they were originally designed as totally distinct entities that did not share a common abstract class. This resulted in code duplication in several places in the back end, as well as in the front-end application. The same issue would be even more noticeable in the planned file sharing functionality. 

Thus, the two entities were rewritten to push the shared fields to an abstract class called `Node`. This way a lot of redundancy was removed and the code became clearer. In the end, the redesign was beneficial for the refactoring to CQRS too.



