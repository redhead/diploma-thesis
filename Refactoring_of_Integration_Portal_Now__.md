## Refactoring of Integration Portal

Now that all the principles, concepts, and tools were settled and explained, the whole process of refactoring the Integration Portal application to the CQRS/ES design is presented. This section includes describing the preparation and strategy chosen for the task, the sole evolution of the refactoring, and propositions of solutions to some problems that occurred in the process. In conclusion, a few ideas for the future extensions of this design to implement new features is suggested.

### Preparation

In the preparation to the refactoring, it was needed to go through the whole back-end code to get knowledge of all the features and functionality that would be subjected to the refactoring. In the process it was found out that some of the code needs to be revised to better fit the CQRS design.

The first thing was to to untangle the dependencies of the components and layers to find out how they are connected and how it affects the refactoring. In the original design, where CQRS and ES principles were not taken into account, some of the code aspects were believed to cause trouble in the refactoring. Because of that, the code underwent some changes before the refactoring.

Concurrently, development of a new feature was considered to extend the functionality in the original code. The functionality was to allow users to share their files with other users, which was not possible before. On top of that, ability to specify permissions on the shared files for each user (group) was to be developed too. The time of completion of this functionality was expected to be some time during the refactoring and thus it was originally intended to be subjected to the refactoring as well.

