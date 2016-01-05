## Testing

As stated in the description of the refactoring strategy, the whole process was continually validated by tests written using the test-driven development methodology. The strategy was to write the tests first and then perform refactoring on the code to make the tests pass. As the refactoring was gradual, one domain entity at the time, the particular tests were written before each part of the refactoring. 

Although, the tests were written from scratch, they were based on the scenarios found in the integration tests for the original Integration Portal code. The tests followed the following scheme --- set up the initial state for the test, dispatch a command, whose processing was under test, and finally assert the correct state of the database (of the read model effectively). Listing X **reference needed** shows an example of one of the tests. The tests were written in the Groovy programming language \cite{groovy} by using Spock library \cite{spock}, a testing and specification framework.

	public class CreateLabelIntegrationSpec 
			extends AbstractIntegrationSpecification {

	    def "should create a label"() {
	        when:
	            dispatch new CreateLabelCommand(LabelId.of("1"), "work", "red")

	        then:
	            def label = get(Label, "1")
	            label.id == "1"
	            label.name == "work"
	            label.color == "red"
	            label.owner.id == "1"
	    }

	    def "creating duplicate label results in error"() {
	        when:
	            dispatch new CreateLabelCommand(LabelId.of("1"), "work", "red")
	            dispatch new CreateLabelCommand(LabelId.of("2"), "work", "red")

	        then:
	            thrown(AlreadyExistsException)
	    }

	}

As you can see, the tests are easy to navigate by leveraging Groovy's named blocks that refer to the BDD (Behavior Driver Development) style of writing tests, which is a feature of Spock Framework. A test method usually follows the given-when-then paradigm, where the given block sets up the environment for the test, the when block executes the code under test, and the then block validates the test assertions.

All the integration tests written for the refactoring follow the same scheme:

1. Optionally, set up the environment of the test scenario by dispatching commands
2. Dispatch the command(s) under test
3. Assert that
	a) the state of the database is correct by querying the Hibernate entity manager, or
	b) an expected exception is thrown

The `dispatch()` method is a convenience method to send commands to the command gateway in a test. The command gateway and the command bus then processes the commands by executing a command handler as usual. If new events are published by handling the commands, they are  processed by event listeners and the read model is built accordingly. Finally, the tests assert the state of the database by querying the Hibernate entity manager and checking the attribute values in the entities. In some cases, like in the listing X**reference needed** above, throwing of an expected exception is needed to assert.

The code under test uses Hibernate to access the database in a uniform way, regardless of the database type. For the test environment, an embedded in-memory HSQL database is used to save resources and make the tests run faster. Setting up the in-memory database is just a matter of configuration of the test environment.