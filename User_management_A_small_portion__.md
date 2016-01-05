### User management

A small portion of the application was subjected to more than refactoring to improve the user experience with the system. The portion was related to user management, specifically registration and password restoration. The original code didn't provide a great experience for that functionality --- the registration of externs had to be done by administrators that had to set up users including the passwords which must have been sent by e-mail (or other means) to each person manually. This way was unreliable and put more weight on the administrators, it also undermined security as the passwords were known to the administrators sending them to the users. Sagas were a perfect fit to redesign this portion of the system.

The first part, the registration process, in which a new user is created in the system, was flawed by the missing e-mail address in the entity of the user. Thus, the password could not be sent automatically to the user. Usually, an e-mail address is crucial for user verification and password restoration. So, the implementation was altered and the user e-mail address must be included when registering a new user. If the command succeeds and the new user is created, the user needs to verify their e-mail address to set up a password. A saga that listens for the `UserCreatedEvent` manages this business transactions and waits for the user to verify their e-mail address. The whole process of the saga is defined by the following steps:

1. A user with an e-mail address is created in the state of needed e-mail verification.
2. The event starts a saga that creates a new random registration token for the user. This token is sent to the user's e-mail address.
3. Using an event scheduler, an event is scheduled which expires the token in one hour.
4. Finally,
    a) During that time, the user sets their password by using the token. The registration is complete and the user is verified.
    b) The expire event is published and the token is invalidated. The user needs to request a new registration token to be able to set the password.
<ol type="a">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ol>
TODO list formatting

Password restoration was designed similarly. When users forget their password, the e-mail address is the only trusted communication channel (since the verification above) to handle the restoration, i.e. setting a new password. To manage that process, a different saga, which follows the steps below, is created:

1. A user requests restoration of forgotten password in the UI.
2. The saga is started and a new random restoration token is created for the user. This token is sent to the user's e-mail address.
3. Using an event scheduler, an event is scheduled which expires the token in one hour.
4a. During that time, the user sets their password by using the token. The restoration is complete and the user can log in.
4b. The expire event is published and the token is invalidated. The user needs to request a new restoration token to be able to set the password.

As it turned out, the two processes were so similar, that the sagas could share a great portion of the code in a common base class, `AbstractExpirableUserTokenSaga`. The specialized classes for each saga --- `UserEmailVerificationSaga`, and `RestoreLostUserPasswordSaga` --- differ in only the types of the events they listen to or schedule.

Even though the implementation of the sagas to support both of these processes is ready, the functionality is put on hold until the mail server and the corresponding infrastructure to send automated e-mails is provided.