### Future development

To build further upon the system featuring CQRS and event sourcing, a few ideas for the future developers are presented below to explore.

#### Axon 3

Version 3 of Axon Framework is under development targeting a release date in the first quarter of 2016. It brings a refined way of writing classes of aggregates and sagas, so they do not extend any Axon's classes making the domain model clean of implementation details. It also brings other interesting features such as scheduled commands and aggregates being able to create other aggregate instances. All these new features could be beneficial to the Integration Portal.

#### Separation to modules

As the code written in Axon Framework is nicely decoupled, the project could be broken into smaller modules, each dealing with some specific subdomain. Possibly, each bounded context could be separated to an individual submodule. This approach can be further evolved into microservices and better scalability.

#### Notifications

For better user experience on the front-end side of Integration Portal, a notification system based on event handlers could be developed. Notifications about various events relevant to users could be stored in the database and shown when users open Integration Portal in their browsers. On top of that, a real-time push notifications from the server to the client can be achieved by using WebSockets or ServiceWorkers. The same principle could be used to send notifications through e-mail messages.

#### History of file/folder changes

One feature that was planned from the beginning of the development of Integration Portal was to support a history of changes made to a file or a folder. This log would include information what was done to a node and by who. More importantly, files should provide a list of all the versions of the file data and a way to restore the old versions.

Because all the events about changes in node aggregates are now stored, it is possible to read these events and build a read model to support this kind of a history log. This, however, does not apply to restoring back a specific version of a file, because the events do not contain the data about the content. The reason is that the events would become too large and would decrease the system performance significantly. One workaround would be to store the data in another storage, and events would refer to it by the aggregate identifier and a version, for example.