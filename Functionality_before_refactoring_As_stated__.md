### Functionality before refactoring

As stated previously, at the time of starting the refactoring to CQRS and ES the project was still in development and some functionality was not yet implemented. Some features were planned for implementation in the future. The domain model described above illustrates the back-end domain entities before refactoring. The implemented functionality around these entities at that time is presented below.

#### User management

- User authentication with username and password (no FELid integration yet)
- Authorization based on permissions to
    - access to the main administration (users, user roles)
    - edit organizational units
    - edit externs
    - edit password
- Password change
- Group management
- User role management

#### Files

- Uploading (data being stored in CESNET infrastructure)
- Renaming
- Moving to other folders
- Removing
- Downloading the file contents
- Updating the file contents
- Switching to online / offline medium in CESNET

#### Folders
- Creating
- Renaming
- Moving to other folders
- Removing (along with all their child files and folders)
- Switching to online / offline in CESNET

#### Labels
- Creating
- Modifying (text, color)
- Deleting
- Adding and removing labels on files and folders
- Filtering files and folders by a label

#### Organizational Units
- Modifying (name, quota)
- Assigning organizational unit administrators

### Missing functionality
- Sharing files with other users
- Enforcing the storage capacity limit of organizational units by their quota
- KOSapi integration
- FELid integration
