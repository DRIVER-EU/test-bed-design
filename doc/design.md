# Test-bed design

The test-bed is designed to fulfil the [functional requirements](https://driver-eu.gitbooks.io/test-bed-specification/technical-requirements.html). Clearly, different designs can be created that all fulfil these requirements, so this chapter provides a brief explanation of the major design decisions that underlie the current test-bed's reference implementation. Its intended audience is core developers, who want to improve its functionality, or other developers, who want to create an alternative test-bed that also satisfies these requirements.

## Lessons learned from the Functional Specification

Part of the functional specification describes the [lessons learned](https://driver-eu.gitbooks.io/test-bed-specification/content/lessons-learned.html) from the past. To summarize the most important technical lessons:

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

Besides Apache Kafka, there are numerous popular open source messaging systems that were considered: [ActiveMQ](activemq.apache.org), [RabbitMQ](https://www.rabbitmq.com), and [ZeroMQ](http://zeromq.org). The main reason for using Kafka, however, is its speed, low latency, and the fact that it is built from the ground up to be distributed. Especially for the simulators that are connected to the test-bed, speed and low-latency are very important. And Kafka can easily process up to 100,000 messages per second, 10 times as much as the others. Its distributed nature allows to not only separate simulators and solutions, if required, but also supports having a reliable cross-site communication framework.

## Well-defined messages using Apache AVRO

Being able to communicate using well-defined messages is of primordial importance for any messaging system, and the test-bed employs Apache AVRO. Other candidates for defining messages were [XML schema](https://en.wikipedia.org/wiki/XML_schema), Google's [Protobuf](https://github.com/google/protobuf), and HLA.
- XML schema is arguably the most popular format, especially when dealing with standards. However, it is very verbose, being text-based, which makes all messages very large. And it does not allow for schema migration, i.e. different versions of a message.
- Protobuf is also a binary message format, like AVRO, but also lacks schema migration.
- HLA, yet another binary message format, is standard in the (Defence) simulation world, but of no use when defining messages in the CIS space.

## Dealing with large messages



## CIS and CSS adapters

On top of the communication framework, a set of guidelines need to be present that allows for external components and Test-bed components to communicate effectively. In this case, this set of guidelines is packed into a simple library called the adapter.

The adapter is the only form of connection between an application (solution, simulator, gateway, or otherwise) and the communication framework. There should be no other way to connect to it.

The adapter sets up and maintains the connection between application and the communication framework via several system message topics. The adapter deals with error handling and additional message validation, allowing the application to send and receive messages easily.

Since the Test-bed requires multiple solutions and simulators with different implementation frameworks to be connected to the CIS/CSS, multiple adapters are created. Currently, an adapter for Java, JavaScript/TypeScript and C# is created and can be found in the DRIVER+ GitHub (https://github.com/DRIVER-EU). A REST endpoint should also be present for web services to communicate with the CSS in a similar fashion. All adapters should have similarly behaving functionality with a clear and understandable API.


## Adapters in several programming languages

## Overview of the supportive tooling, from the open source community and D+, and how they fit together

## Dealing with simulations

Time management, Injects, Relation to simulation standards DIS and HLA, Other simulators



## Dealing with data

Map data, Other data

