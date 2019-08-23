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

## Annex 3.5 Security architecture

The testbed Security Architecture consists of the following elements:

- Security Services.
- CIS/CSS broker security features.
- CIS/CSS adapter security features.

For more details, read on the next sections.

### Security Services 

There are two security services running in the test-bed:

- Certificate Management Service.
- Authorization Service.

#### Certificate Management Service

The Certificate Management Service provides:

- A Certificate Authority (CA) that issues SSL/TLS client (resp. server) certificates to the CIS/CSS adapters (resp. broker).
- A REST API - backed by the aforementioned CA - for managing the lifecycle of X.509 (standard) certificates used for SSL/TLS authentication in client-server communications on the testbed. This is the API used by the Admin Tool on behalf of system administrators, to issue SSL client certificates certificates to developers for their adapters. The API is described on the [test-bed's GitHub repository](https://github.com/DRIVER-EU/test-bed/tree/master/docker/local%2Bsecurity#certificate-management-service).
- An endpoint for verifying certificate revocation status (CRL or OCSP standard). This is the endpoint used by the CIS/CSS broker and adapters to validate the status of the certificate of any remote peer they are establishing a SSL session with.

The Certificate Management Service is based on the open source project EJBCA (Community Edition).

#### Authorization Service

The Authorization Service provides two endpoints:

- PAP (Policy Administration Point).
- PDP (Policy Decision Point).

The Authorization Service is based on the open source XACML 3.0 PDP implementation project [AuthzForce](https://github.com/authzforce/core) (Thales).

##### Policy Administration Point

The PAP (Policy Administration Point in [ABAC (Attribute-Based Access Control)](https://csrc.nist.gov/publications/detail/sp/800-162/final) standard model) endpoint exposes a REST API for managing CIS/CSS topic access policies. System administrators interact via the Admin Tool with this API for defining and managing the access policies for CIS/CSS topics. The API is described on the [Authorization Service's GitHub repository](https://github.com/DRIVER-EU/test-bed-security-authorization-service#api-usage).

##### Policy Decision Point

The PDP (Policy Decision Point in [ABAC (Attribute-Based Access Control)](https://csrc.nist.gov/publications/detail/sp/800-162/final) standard model) exposes a REST API for requesting an authorization decision (Permit/Deny) according to the policies defined via the PAP, and an input authorization request. The CIS/CSS broker (via its PEP, aka Kafka *authorizer* in this case) interacts with this API for deciding whether to reject (if PDP returns Deny) or let a message go (if PDP returns Permit) through the broker. We say that the CIS/CSS broker enforces the PDP's decision. 

The PDP interface complies with the standards:

- [REST Profile of XACML 3.0](https://docs.oasis-open.org/xacml/xacml-rest/v1.0/xacml-rest-v1.0.html) for the transport protocol (HTTP) and entry point;
- [JSON Profile of XACML 3.0](https://docs.oasis-open.org/xacml/xacml-json-http/v1.0/xacml-json-http-v1.0.html) for the API request-response payload formats (XACML Request/Response).

### CIS/CSS broker security (Kafka)

The broker security consists of the following:

- Kafka SSL/TLS layer.
- DRIVER+'s custom Kafka *authorizer* (XACML-driven).

#### Kafka TLS layer

*The TLS term is used instead of *SSL* because we mandate the use of TLS 1.0 or later in DRIVER+.*

The Kafka broker exposes a TLS server interface that enforces the confidentiality, integrity and mutual authentication of communications with CIS/CSS adapters. It authenticates to Kafka consumers with a TLS server certificate issued by the Certificate Management Service's CA mentioned earlier. The Kafka broker is configure to require TLS client certificate authentication with client certificates issued by that same CA. The TLS setup follows the official Kafka documentation's section: [Encryption and Authentication with SSL](https://docs.confluent.io/current/kafka/authentication_ssl.html).

#### Custom Kafka Authorizer (XACML PEP)

The Kafka broker's configuration is modified to use a [custom Authorizer](https://docs.confluent.io/current/kafka/authorization.html#authorizer) implementation that can interact with the Authorization Service's PDP endpoint mentioned earlier. This Authorizer plays the role of PEP (Policy Enforcement Point) in the [ABAC (Attribute-Based Access Control)](https://csrc.nist.gov/publications/detail/sp/800-162/final) standard model, and more specifically in the [XACML standard](https://docs.oasis-open.org/xacml/3.0/xacml-3.0-core-spec-en.html) architecture. Therefore, this custom Authorizer supports the required XACML standards (REST and JSON profiles) and rejects (resp. accepts) messages if the PDP's returned decision is Deny (resp. Permit).

In order to improve performances, this Authorizer may cache decisions to avoid requesting the remote PDP every time. The downside is that changes to the topic access policies that occur in the remote PDP during the cache interval may be ignored. This decision cache is enabled and configured via Docker Compose environment variables.

The Authorizer's source code is open and available with technical documentation on a [dedicated GitHub repository](https://github.com/DRIVER-EU/kafka-combined-acl-xacml-authorizer).

### CIS/CSS adapter security

CIS/CSS adapters are Kafka clients. Therefore, they are required to use TLS (1.0 or later) for communicating with the CIS/CSS Kafka brokers, and TLS client certificates (X.509) provided by the aforementioned Certificate Management Service. In order to properly authenticate the CIS/CSS broker, adapters must use a TLS truststore (set of trusted CAs) that contains the certificate of the Certificate Management Service's backend CA, or preferably of the Root CA (that the backend CA is a subordinate of).

The Kafka documentation gives an example of [Kafka client SSL configuration](https://kafka.apache.org/documentation/#security_configclients).
