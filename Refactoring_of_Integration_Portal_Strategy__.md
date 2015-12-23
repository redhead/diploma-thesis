## Refactoring of Integration Portal

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

## Comparison to layered architecture