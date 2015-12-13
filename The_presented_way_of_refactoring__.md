The presented way of refactoring was applied similarly to other aspects of the original domain model. This resulted in a number of new aggregates that use event sourcing as their backing repository to take care of business logic and upholding the invariants. Event processing then ensured that the read model is built accordingly.

