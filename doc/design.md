# Test-bed design

## Lessons learned from the Functional Spec

## Distributed message bus using Apache Kafka

### Technical framework

From a technical perspective the Common Information Space (CIS) and Common Simulation Space (CSS) is a basic communication framework with several internal tools to easily monitor and debug the framework and the messages being sent over the framework.

### Kafka

For the implementation of the backbone of the CIS and CSS, the communication framework, [Apache Kafka](https://kafka.apache.org) is chosen. The main reasons to use Kafka are:
- Kafka allows for high performance sending and receiving of a very large number of messages
- Kafka allows for fast data replication and supports multiple receivers on the same message topic
- Kafka is a highly durable messaging system, persisting messages on the server or complete distributable file systems
- Kafka is a distributed system, making it scalable in the amount of message topics, senders and receivers.
- Kafka already has a large developer community, making it possible to easily use community-released tools to the current framework and assuring sustainability of Kafka.
- Kafka already has several security and message validation modules present, that will make it easier for simulators to safely connect to the CSS.

### CIS and CSS adapters

On top of the communication framework, a set of guidelines need to be present that allows for external components and Test-bed components to communicate effectively. In this case, this set of guidelines is packed into a simple library called the adapter.

The adapter is the only form of connection between an application (solution, simulator, gateway, or otherwise) and the communication framework. There should be no other way to connect to it.

The adapter sets up and maintains the connection between application and the communication framework via several system message topics. The adapter deals with error handling and additional message validation, allowing the application to send and receive messages easily.

Since the Test-bed requires multiple solutions and simulators with different implementation frameworks to be connected to the CIS/CSS, multiple adapters are created. Currently, an adapter for Java, JavaScript/TypeScript and C# is created and can be found in the DRIVER+ GitHub (https://github.com/DRIVER-EU). A REST endpoint should also be present for web services to communicate with the CSS in a similar fashion. All adapters should have similarly behaving functionality with a clear and understandable API.

## Well-defined messages using Apache AVRO

Dealing with large messages

## Adapters in several programming languages

## Overview of the supportive tooling, from the open source community and D+, and how they fit together

## Dealing with simulations

Time management, Injects, Relation to simulation standards DIS and HLA, Other simulators



## Dealing with data

Map data, Other data

