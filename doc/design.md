# Test-bed design

The test-bed is designed to fulfil the [functional requirements](https://driver-eu.gitbooks.io/test-bed-specification/technical-requirements.html). Clearly, different designs can be created that all fulfil these requirements, so this chapter provides a brief explanation of the major design decisions that underlie the current test-bed's reference implementation. Its intended audience is core developers, who want to improve its functionality, or other backend developers, who want to create an alternative test-bed that also satisfies these requirements.

## Lessons learned from the Functional Specification

Part of the functional specification describes the [lessons learned](https://driver-eu.gitbooks.io/test-bed-specification/content/lessons-learned.html) from the past. To summarize the most important technical lessons that have influenced the current design significantly, are:

1. The test-bed should be open source.
2. The test-bed should have a message-oriented architecture.
3. The test-bed should use well-defined, easily accessible, syntactically correct messages, and close to common standards.
4. The test-bed must be easily reproducible, and offer administrative as well as supporting tools and services.

## Distributed message bus using Apache Kafka

Based on the functional requirements and lessons 1 and 2, and an analysis of many existing message-oriented systems, the test-bed's backbone is built upon the distributed streaming service, [Apache Kafka](https://kafka.apache.org). The main reasons to use Kafka are:
- Kafka allows for high performance sending and receiving of a very large number of messages
- Kafka allows for fast data replication and supports multiple receivers on the same message topic
- Kafka is a highly durable messaging system, persisting messages on the server or complete distributable file systems
- Kafka is a distributed system, making it scalable in the amount of message topics, senders and receivers.
- Kafka already has a large developer community, making it possible to easily use community-released tools to the current framework and assuring sustainability of Kafka.
- Kafka already has several security and message validation modules present, that will make it easier for simulators to safely connect to the CSS.

Besides Apache Kafka, there are numerous popular open source messaging systems that were considered: [ActiveMQ](activemq.apache.org), [RabbitMQ](https://www.rabbitmq.com), and [ZeroMQ](http://zeromq.org). The main reason for using Kafka, however, is its speed, low latency, and the fact that it is built from the ground up to be distributed. Especially for the simulators that are connected to the test-bed, speed and low-latency are very important. And Kafka can easily process up to 100,000 messages per second, 10 times as much as the others. Its distributed nature allows to not only separate simulators and solutions, if required, but also supports having a reliable cross-site communication framework. Additionally, with its schema registry, it has excellent support for message validation out-of-the-box, which is detailed in the next section. The same applies to message persistency. Each message is immediately persisted to disk for a set time, which is easy for After-Action-Reviews, but also for clients that are not continuously online. In most messaging systems, when a consumer is briefly offline, the message is lost forever unless special care is taken to persist them.

## Well-defined messages using Apache AVRO #{AVRO}

Being able to communicate using well-defined messages is of primordial importance for any messaging system, and the test-bed uses [Apache AVRO](https://avro.apache.org).

Avro provides:

- Rich data structures.
- A compact, fast, binary data format.
- A container file, to store persistent data. For example, you could save all logged AVRO messages into a file, which also includes the schema file. This means that you will always be able to read the file at a later date, as is contains all the information to decode the messages.
- Remote procedure call (RPC).
- Simple integration with dynamic languages. Code generation is not required to read or write data files nor to use or implement RPC protocols. Code generation as an optional optimization, only worth implementing for statically typed languages.

So in our test-bed, each message consists of a key and a value. Each message uses:
- The **same** AVRO-encoded [key](https://github.com/DRIVER-EU/avro-schemas/blob/master/edxl-de/edxl-de-key.avsc), based on the core attributes from the EDXL DE envelope (distributionID, senderID, dateTimeSent, dateTimeExpires, distributionStatus, and distributionKind). It can be used to support easy filtering and routing, and have a consistent message timestamp.
- A potentially different AVRO-encoded value, containing the actual message.

Each adapter verifies the key and value of each message before publishing (producing in Kafka terms) it. As Kafka limits you to have only one key/value schema pair per topic, there is no confusion when consuming a message about the schema's to use to decode a message's key and value.

Other evaluated candidates for defining message formats were [XML schema](https://en.wikipedia.org/wiki/XML_schema), Google's [Protobuf](https://github.com/google/protobuf), and HLA.
- XML schema is arguably the most popular format, especially when dealing with standards. However, it is very verbose, being text-based, which makes all messages very large. And it does not allow for schema migration, i.e. different versions of a message. This often leads to a 'creative re-use' of existing fields, which is common practice but not recommended.
- Protobuf is also a binary message format, like AVRO, but also lacks schema migration.
- HLA, yet another binary message format, is standard in the (Defence) simulation world, but of no use when defining messages in the CIS space.

## Dealing with large messages

Kafka and AVRO are ideally suited for smaller-sized messages, i.e. typically not exceeding 1Mb compressed. However, a typical flooding file may well exceed 1Gb of data. The solution that currently is being designed is the following: in the test-bed, there is one file hosting data services (e.g. FTP) for uploading large files. Each adapter, when confronted with a large message exceeding a threshold, uploads the data in the background to the file hosting data service, and in return, receives an obfuscated link. This link is subsequently shared via the test-bed, and any consumer interested in the actual data can retrieve it from there.

A note about security: In principal, these files are openly available within the Trial environment (which may be on a closed network, of course). However, since the link is not easy recognizable, you will not be able to guess it. Basically, *security through obscurity*. This approach is similar to the one popular open file sharing services provide like [wetransfer.com](http://wetransfer.com), and should be sufficiently safe for current usage.


## CIS and CSS adapters in several programming languages

On top of the communication framework, a set of guidelines need to be present that allows for external components and Test-bed components to communicate effectively. In this case, this set of guidelines is packed into a simple library called the adapter.

The adapter is the only form of connection between an application (solution, simulator, gateway, or otherwise) and the Kafka communication framework. There should be no other way to connect to it.

The adapter sets up and maintains the connection between application and the communication framework via several system message topics. The adapter deals with error handling and additional message validation, allowing the application to send and receive messages easily.

Since the Test-bed requires multiple solutions and simulators with different implementation frameworks to be connected to the CIS/CSS, multiple adapters are created. Currently, an adapter for [Java](https://github.com/DRIVER-EU/java-test-bed-adapter), [JavaScript/TypeScript](https://github.com/DRIVER-EU/node-test-bed-adapter) and [C#](https://github.com/DRIVER-EU/csharp-test-bed-adapter) is created and can be found in the DRIVER+ GitHub (https://github.com/DRIVER-EU). A [REST](https://github.com/DRIVER-EU/test-bed-rest-service) endpoint is also present for web services to communicate with the CSS in a similar fashion. All adapters should have similarly behaving functionality with a clear and understandable API.

Adapters extend regular Kafka connectors with the following information, each of which is displayed in the test-bed's admin tool:
- *Heartbeat signals:* Before you can start a Trial, every solution, simulator and tool needs to be up-and-running. Therefore every adapter transmits a heartbeat signal every 5 seconds to inform others it is online.
- *Logging:* Besides being online, it is also important to know that each connected service is running as expected, so each adapter offers the option to log warnings/errors to the test-bed as well.
- *Configuration options:* The adapter can inform others to what topics it subscribes and publishes. In addition, this can be configured too externally. For example, the admin tool can configure the (potentially secret) topics an adapter must listen too.
- *Time:* A Trial scenario typically will not run at real-time, so the adapter needs to share the fictive simulation time. In addition, it shares the simulation speed, as we may be running slower or faster than real-time, as well as the simulation state.
