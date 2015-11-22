In the following paragraphs, a brief description of the entities is presented.

#### File
An entity that represents an uploaded file into the system. It's not meant to carry the file contents, but it holds its meta data (file name, size, content type, etc.). In the implementation this entity is called `FileMetadata` for historic reasons, but `File` better captures that is represents a file in the simulated file system.

#### Folder
The `Folder` entity is a way of organizing `File`s into named groups and is equivalent to folders in regular file systems. A folder can contain files and also other folders effectively creating a tree hierarchy of files and folders.

#### User
A `User` entity represents a user granted to access the system and use it to upload and manage files and to organize them in folders. The files the user uploads and the folders they create are owned by that user.

#### Label
Label is a special way