### Functionality before refactoring

As stated previously, at the time of starting the refactoring to CQRS and ES the project was still in development and some functionality was not yet implemented. Some features were planned for implementation in the future. The domain model described above illustrates the back-end domain entities before refactoring. The implemented functionality around these entities at that time is presented below.

#### User management

- User authentication with username and password (no FELid integration yet)
- Authorization based on permissions
    - access to main administration (users, user roles)
    - edit organizational units
    - edit externists
    - edit password
- Password change
- Group management

#### Files

- Uploading (data being stored in CESNET infrastructure)
- Renaming
- Moving
- Removing
- Updating the file contents
- Labeling

#### Folders
- Creating
- Renaming
- Removing (along with all their child files and folders)
- Labeling

#### Organizational Units
- Modifying (name, quota)
- Assigning organizational unit administrators

#### CESNET integration

### Missing functionality
- Sharing files with other users
- Enforcing the storage capacity limit of organizational units by their quota
- KOSapi integration
- FELid integration
