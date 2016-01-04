## Axon Framework

In this section, let's introduce the reader to Axon Framework's specifics that relate to the refactoring to CQRS and ES further in the text. For more details, see the framework's documentation **citation needed**.

As mentioned previously, Axon Framework very well supports Spring Framework, which is also used in Integration Portal. It is not required to use Spring Framework in order to use Axon Framework, however, it greatly simplifies the configuration. Axon provides namespace support for Spring XML configuration of the Application Context to set up the Axon's building blocks.

Axon Framework doesn't hide the CQRS details from its users and so it shares the CQRS terminology. The building blocks described in CHAPTER x.y.z **reference needed** are used to build the domain model with Axon Framework. On top of these building blocks, some infrastructure and configuration is needed to set up Axon. The following paragraphs describe the concepts that are specific to Axon Framework implementation.





