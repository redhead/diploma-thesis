
### RESTful vs CQRS and DDD

One of the concerns right at the beginning of the refactoring was the REST interface design and its suitability for CQRS guided by DDD, especially regarding the PUT method. The reasons were explained in detail in~\ref{revising-the-rest-interface}~\nameref{revising-the-rest-interface}. The original plan was not to modify the RESTful interface at all and find a way to determine what the intent of the PUT request is on the server (essentially what task needs to be done). Few ideas were proposed:

1. The server determines which fields were changed on the resource, creates appropriate commands (e.g. `RenameFileCommand`, `MoveFileCommand`, ...) and dispatches these individually. 
2. The server dispatches a general command (e.g. `UpdateFolderCommand`) and the aggregate determines which fields were changed and sends individual events instead (`FolderRenamedEvent`, `FolderMovedEvent`, etc.).
3. Because the UI is task-based and it knows which task it requests (move, rename, add, etc.), every PUT request could specify a special HTTP header that would define what command.

However, there were some problems with each of these scenarios (each entry of the following list relates to the respective proposition above):

1. If each command is dispatched individually, any of them can fail leaving others out of transaction and thus out of atomic change to the REST resource (which is bad by RESTful principles).
2. It uses a general-purpose update command, and so the aggregates would become CRUD-like (regardless of individual events).
3. This tightly couples the UI with the command handling on the server, which is an implementation detail of CQRS. Also, if the REST interface is used by clients other than the UI, it lays a lot of unnecessary knowledge on them.

To resolve the issue, it was decided to ask on StackOverflow for help \cite{stackoverflow}. One other proposition was

This one I don't like at all: In the request to the server I would specify in the headers which command to use, because the UI is still task based (but communication is done via REST). However it will fail in any other use of REST communication (e.g. in external apps) as they are not bound to change only the one field in one request. Also I bring quite a big coupling into the UI, REST, and ES-based backend.