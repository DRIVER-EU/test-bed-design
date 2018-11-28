# Annex 3. Test-bed design

The Test-bed is designed to fulfil the functional requirements, as described in D923.11, [Functional specification of the Test-bed](https://www.driver-project.eu/wp-content/uploads/2018/08/DRIVERPLUS_D923.11_Functional-Specification-of-the-Test-bed.pdf). Clearly, different designs can be created that all fulfil these requirements, so this chapter provides a brief explanation of the major design decisions that underlie the current Test-bed's reference implementation. Its intended audience is core developers, who want to improve its functionality, or other backend developers, who want to create an alternative Test-bed that also satisfies these requirements.

## Annex 3.1 Lessons learned from the Functional Specification

Part of the functional specification describes the [lessons learned](https://driver-eu.gitbooks.io/test-bed-specification/content/lessons-learned.html) from D923.11. To summarize the most important technical lessons that have influenced the current design significantly, are:

1. The Test-bed should be open source.
2. The Test-bed should have a message-oriented architecture.
3. The Test-bed should use well-defined, easily accessible, syntactically correct messages, and close to common standards.
4. The Test-bed should be easily reproducible, and offer administrative as well as supporting tools and services.

## Annex 3.2 Distributed message bus using Apache Kafka

Based on the functional requirements and lessons 1 and 2, and an analysis of many existing message-oriented systems, the Test-bed's backbone is built upon the distributed streaming service, [Apache Kafka](https://kafka.apache.org). The main reasons to use Kafka are:

- Kafka allows for high performance sending and receiving of a very large number of messages.
- Kafka allows for fast data replication and supports multiple receivers on the same message topic.
- Kafka is a highly durable messaging system, persisting messages on the server or complete distributable file systems.
- Kafka is a distributed system, making it scalable in the amount of message topics, senders and receivers.
- Kafka already has a large developer community, making it possible to easily use community-released tools to the current framework and assuring sustainability of Kafka.
- Kafka already has several security and message validation modules present, that will make it easier for simulators to safely connect to the CSS.

Besides Apache Kafka, there are numerous popular open source messaging systems that were considered: [ActiveMQ](activemq.apache.org), [RabbitMQ](https://www.rabbitmq.com), and [ZeroMQ](http://zeromq.org). The main reason for using Kafka, however, is its speed, low latency, and the fact that it is built from the ground up to be distributed. Especially for the simulators that are connected to the Test-bed, speed and low-latency are very important. And Kafka can easily process up to 100,000 messages per second, 10 times as much as the others. Its distributed nature allows to not only separate simulators and solutions, if required, but also supports having a reliable cross-site communication framework. Additionally, with its schema registry, it has excellent support for message validation out-of-the-box, which is detailed in the next section. The same applies to message persistency. Each message is immediately persisted to disk for a set time, which is easy for After-Action-Reviews, but also for clients that are not continuously online. In most messaging systems, when a consumer is briefly offline, the message is lost forever unless special care is taken to persist them.

## Annex 3.3 Well-defined messages using Apache AVRO {#AVRO}

Being able to communicate using well-defined messages is of primordial importance for any messaging system, and the Test-bed uses [Apache AVRO](https://avro.apache.org).

AVRO provides:

- Rich data structures.
- A compact, fast, binary data format.
- A container file, to store persistent data. For example, you could save all logged AVRO messages into a file, which also includes the schema file. This means that you will always be able to read the file at a later date, as is contains all the information to decode the messages.
- Remote procedure call (RPC).
- Simple integration with dynamic languages. Code generation is not required to read or write data files nor to use or implement RPC protocols. Code generation as an optional optimization, only worth implementing for statically typed languages.

In the Test-bed, each message consists of a key and a value, with:

- The **same** AVRO-encoded [key](https://github.com/DRIVER-EU/avro-schemas/blob/master/edxl-de/edxl-de-key.avsc), based on the core attributes from the EDXL DE envelope (distributionID, senderID, dateTimeSent, dateTimeExpires, distributionStatus, and distributionKind). It can be used to support easy filtering and routing, and have a consistent message timestamp.
- A potentially different AVRO-encoded value, containing the actual message.

Each adapter verifies the key and value of each message before publishing (producing in Kafka terms) it. As Kafka limits you to have only one key/value schema pair per topic, there is no confusion when consuming a message about the schema's to use to decode a message's key and value.

Other evaluated candidates for defining message formats were [XML schema](https://en.wikipedia.org/wiki/XML_schema), Google's [Protobuf](https://github.com/google/protobuf), and HLA.

- XML schema is arguably the most popular format, especially when dealing with standards. However, it is very verbose, being text-based, which makes all messages very large. And it does not allow for schema migration, i.e. different versions of a message. This often leads to a 'creative re-use' of existing fields, which is common practice but not recommended.
- Protobuf is also a binary message format, like AVRO, but also lacks schema migration.
- HLA, yet another binary message format, is standard in the (Defence) simulation world, but of no use when defining messages in the CIS space.

## Annex 3.4 CIS and CSS adapters in several programming languages

On top of the communication framework, a set of guidelines need to be present that allows for external components and Test-bed components to communicate effectively. In this case, this set of guidelines is packed into a simple library called the adapter.

The adapter is the only form of connection between an application (solution, simulator, gateway, or otherwise) and the Kafka communication framework. There should be no other way to connect to it.

The adapter sets up and maintains the connection between application and the communication framework via several system message topics. The adapter deals with error handling and additional message validation, allowing the application to send and receive messages easily.

To read more about the implementation, see [Chapter 2 Adapters](test-bed-description.md#adapters).
