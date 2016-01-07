

#### Task-based user interface

In a typical three-layer architecture or simple CRUD (Create, Read, Update, Delete) systems, the service layer translates its data models to DTOs (Data Transfer Objects) and passes them to the user in the form of user interface (UI). The user modifies the data and sends it back to the service layer for persistence. The service layer updates its data models and stores the changes into the data store. This way, passing data objects by using the same update model badly captures the user's intent of the change and usually is in contrast of what domain-driven design teaches us about how to model our domain. Commands in CQRS should reflect their intents, and the same thing can be applied to the UI as well. This term is not related purely to CQRS and is not required or needed. Sometimes a CRUD interface is all that is needed and task-based UI can cause unnecessary complexity. However, developing with DDD and CQRS usually leads to such UI design for complex domains.