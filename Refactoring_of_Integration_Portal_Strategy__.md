## Refactoring of Integration Portal

### Strategy
- one entity/aggregate at the time
- one to one refactoring
- test driven

### Revising the REST interface

- task based API
- task base UI

### Revising the original domain model

- file and folder to abstract parent
- client-side generated IDs
- user emails

### Refactoring the domain model

- domain model
- lombok!
- write model refactoring (services)
- read model refactoring (service)

### Folder deletion saga

### User management sagas


### Resolving the old issues

- unique node name in root
- better user management

### Testing



## Summary

### Benefits

- 
- testing
- removed all the weird code from services (createEntity, deleteEntity)
- enabling of other features

### Disadvantages

- how to model files and folder
- deletion of folders
- harder navigation in code (Axon support for intellij)

### Security
- user passwords


### Suggestions

- bounded contexts
- multiple smaller modules
- not everything must be CQRS / ES

- enabled scaling
- notifications
- read model per screens (spaces)
- history of file changes and diffs (rebuilding)
- support for other file repositories

### Future Development

- Axon 3
- event refactoring, event upcasting

## The future development