## Refactoring of Integration Portal

Now that all the principles, concepts, and tools were settled and explained, the whole process of refactoring the Integration Portal application to the CQRS/ES design is presented. This section includes describing the preparation and strategy chosen for the task, the sole evolution of the refactoring, and propositions of solutions to some problems that occurred in the process. In conclusion, a few ideas for the future extensions of this design to implement new features is suggested.

### Preparation

In the preparation to the refactoring, it was needed to go through the whole back-end code to get knowledge of all the features and functionality that would be subjected to the refactoring. In the process it was found out that some of the code needs to be revised to better fit the CQRS design.

The first thing was to to untangle the dependencies of the components and layers to find out how they are connected and how it affects the refactoring. In the original design, where CQRS and ES principles were not taken into account, some of the code aspects were believed to cause trouble in the refactoring. Because of that, the code underwent some changes before the refactoring.

Concurrently, development of a new feature was considered to extend the functionality in the original code. The functionality was to allow users to share their files with other users, which was not possible before. On top of that, ability to specify permissions on the shared files for each user (or group) was to be developed too. The responsibility of the development was assigned to another member of the Integration Portal team. The time of completion of this functionality was expected to be during the refactoring and thus it was originally intended to be part of the refactoring as well.

### Revising the REST interface

After reading CQRS JOURNEY**citation needed**, the idea of task-based user interface, described in (TASK BASED UI)**reference needed**, was taken into consideration in regards to the Integration Portal UI. A RESTful application programming interface mediates the communication between the front-end user interface and the back-end logic. 

The API was designed according to the typical RESTful concepts. Every entity in the back end (User, Folder, File, etc.) was one resource in the interface. The four basic HTTP methods - GET, POST, PUT, and DELETE - were defined on almost each resource in order for the UI to take actions on the resources. These methods effectively convert to the known CRUD scheme (Create, Read, Update, Delete). The create, read, and delete actions are usually straightforward. The update action, however, can sometimes be complicated. There are often multiple features on a resource that can be updated. With CRUD-based REST interface, it is possible to use the same HTTP method (PUT) to modify multiple different components of a resource with one request (e.g. change a folder name and set new label).

The design of general update command in RESTful API can be problematic when using CQRS, because the intent of the request becomes unclear. There is no real connection between changing a folder name and setting a new label at the same time from the perspective of the user interface (UI). The UI actually never sends a PUT request to alter multiple unrelated features of one resource. So the general update request using the PUT method is exaggerated abstraction that hides the intent of the change. 

To solve this problem, the back-end REST API was redesigned so the update request carries the intent clearly with one component to change at a time.

