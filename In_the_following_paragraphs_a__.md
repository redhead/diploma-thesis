The presented model illustrates the design of the Integration Portal back end at the exact time the the refactoring to CQRS and ES started. Some features of the Integration Portal, as stated in the introduction, are missing, e.g. the sharing functionality and integrations with some systems. The integration with CESNET is present and functional.

In the following paragraphs, a brief description of the entities is presented. 

#### File
An entity that represents an uploaded file into the system. It's not meant to carry the file contents, but it holds its meta data (file name, size, content type, etc.). In the implementation this entity is called `FileMetadata` for historic reasons, but `File` captures better that it represents a file in the simulated file system.

#### Folder
The `Folder` entity is a way of organizing `File`s into named groups and is equivalent to folders in regular file systems. A folder can contain a number of files and also other folders effectively creating a tree hierarchy of files and folders where files are the leaves of the tree.

#### User
A `User` entity represents a user granted to access the system and use it to upload and manage files and to organize them in folders. The files the users upload and the folders they create are owned by that user. At the time the refactoring to CQRS and ES started, there was no sharing functionality, so the users can see only their files and folders and no others in the front end.

#### Label
Users can label files and folders with a short text and a color, they both represent the `Label` entity. This label is displayed by the associated file or folder in the front-end part of the system if the label belongs to the currently logged in user. Users can utilize labels to visually orient themselves within the file system, e.g. by marking the files and folders that relate to "work" or "school" by appropriate text and chosen color. Also, users can search for files and folders with a specific label.

#### Group
The `Group` entity is a way of organizing other users into named groups. These groups are created and owned by users and are meant to be a convenience for the sharing functionality in the future. If users want to share a file or folder with a number of users, they can share it with a group instead. This overcomes the complexity of selecting the users to share with one by one. The groups can represent individual classes or research teams in the university.

#### Organizational Unit
Organizational units map to the departments of the university. The purpose of the `OrganizationUnit` entity is to group users of Integration Portal to their appropriate organizational unit within the university. Each unit is assigned a limit of file storage capacity their users are allowed to utilize. This limit is called a quota. The sum of sizes of all the files owned by the members of an organizational unit must not exceed this quota.

#### User Role and Permisssion
The `UserRole` entity and the `Permission` enumeration form the authorization mechanism used to limit the user's access to various features of the application (e.g. administration). The `UserRole` entity is represented by a name of the role and a collection of permissions. The collection of permissions in a role specifies what permissions a user has if associated with this role. Additionally, a user can be assigned with extra permissions directly without them being associated with any role. This way, it is possible to combine direct permissions and role permissions to create a flexible authorization system. Multiple roles and/or direct permissions can be associated with a user.

