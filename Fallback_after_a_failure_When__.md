
#### Fallback after a failure

When deploying a new version of an application into production for our users, in many cases, it also means that we need to update the RDBMS tables with a new schema and/or update the data. Imagine that after such deployment the system works for only a week before it crashes because of some serious failure. If fixing the system requires a lot of time it is probably best to fall back to the last working version. 

If we use version control systems or other tools to back up the code base it is possible to recover the working code easily. Getting back the database state, however, may be impossible because of the destructive changes made to the production database during deployment. The original data for the old version may be lost forever because of the schema update. If the database was backed up before the deployment we can get the original database back. However, we would lose all the data the users created during the week that the new version was working.

With Event Sourcing, this fallback can be accomplished easily. Since events must never be altered, we still have all the necessary data to recreate the original data model by replaying all the events including those created in that week.

But we can go a step further in our deployment process and keep the two systems, the original version, and the new version, running side by side. After deployment, the new version can take control of generating the events, while both versions can process them individually into their own data models in separate databases. If anything goes wrong with the live version, we can just switch back to the original (but still up-to-date) version running in parallel. This way the users will not be affected by long-lasting down times leaving we more time to fix the problem.

#### Event processing

With a complete event log, we can make projections of the data for the users. But we can also create other systems around the same event log. The systems can then use some or all the events to do tasks like:

- **monitoring** -- for the purpose of statistics regarding, for example, exceptional behavior (e.g. frequency of events or unexpected order of events).
- **fraud detection** -- if a state, which is considered a fraud by some specified rules, is reached, the processor can notify interested people to act upon the situation.
- **data analysis** -- for inspecting the events to get some information valuable for the business, e.g. what action is done the most by the users at what time of a day.
- **data mining** -- to find some new trends and correlations in the data.
- **system integration** -- to merely convey messages and data between multiple systems.

#### Deriving business value from the event log

Event Sourcing can help fulfill new business requirements that involve old data easily. Imagine that the project stakeholders come up with a new requirement for an already working online shopping system. The feature they want to implement is a suggestion mechanism that shows the users the products they removed from their cart just before checking out. Possibly, the users wanted to buy these products but they removed them from the cart because the total order price was too high. The users may want to buy the products next month, though. To suggest those products to the user on their next visit we need the information that the products were removed from the cart just before checkout. If we designed our domain events properly we should have that information for every user in the system already stored as events in the event log. We can build this feature easily using the old events and suggest the right products to each user immediately after the feature deployment. This is a very nice advantage over the systems that store only the current state because these systems do not have the data beforehand. The system wouldn't be able to suggest anything until the next user removes a product from their cart.

A very similar example of a requirement that involves historic data is a chart of price development for a product over time. Many online shopping systems present this kind of chart to their users. It is not possible to (immediately) accomplish this requirement in an application that stores only the current state (the current price of the product). In an event sourced system, this task is easy to accomplish as we probably already have all the events with the price updates for every product.

#### Debugging

We can use the event log to examine a fault in our system. Imagine that a user reports a bug without specifying the steps to reproduce the bug. This is usually a nightmare for software developers isolating the bug. But with Event Sourcing, we have the whole history of events applied to the application and stored in a log. Thus, we can go back in time by replaying the events (similar to model rebuilding) and see what the user did, by looking at the events, at the exact time the bug happened. We can then use standard debugging tools to see what code got executed by the application and why it resulted in producing the bug.

Compare this feature with traditional logging. Logging is a way of saving events about the system execution in a textual, human-readable format to understand the activity of the system and to diagnose problems. However, this format is not very helpful if the log messages are not comprehensive enough, which is completely in the hands of the developers. Also, it is less convenient to restore the system state from these messages because they are, unlike in Event Sourcing, disconnected from the code execution.

#### Testing

**TODO**

### Summary

As you can see, Event Sourcing isn't a complicated idea at all. The premise is that we don't primarily save the current data of an application state, but we instead save all the events that represent the state changes in the system in a persistent log. We then use this log as the *single source of truth* to determine the actual state of the application for various cases.

The concept, however, doesn't inherently say anything about how to get the state or how to see current data of an application. This is a job for the design pattern called CQRS, described in the next section.