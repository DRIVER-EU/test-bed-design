# 4. The Test-bed for developers and system administrators

A system administrator, in the current context, is responsible for installing the Test-bed on their local network, and making sure that all the solution and simulation providers can get access to this network as well. This task is discussed in the use case 'Installing the Test-bed'.

A developer would be tasked with connecting an existing Solution or simulator to the Test-bed. Besides the direct coupling, allowing their tools to receive and publish messages, it most likely also involves translating existing messages to their own format. Finally, in case you are connecting a simulator, you also need more detailed information about the time management in the Test-bed.

Watch the following animation to get a better understanding of what is going on.

<div class="online-only">
<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/299681354" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
</div>

## 4.1 Use case: Installing the Test-bed

The previous chapter already explained how to setup the Test-bed. More detailed information about how to run the Docker-compose environment can be found [here](https://github.com/DRIVER-EU/test-bed/tree/master/docker). Alternatively, use the [GUI](https://driver-eu.github.io/docker-composer) to build your own Test-bed environment in  Docker.

System administrators are also responsible for setting up the local network, such that all solution and simulator providers have access to the local intranet as well as extranet.

In particular, it should be considered that some providers make heavy use of the network, e.g. to download maps, stream video, or access external computer clusters. If that is the case, consider using a throttling service in your network, so one provider does not claim all the network traffic.

More directly related to the Test-bed, however, is the connection of all solutions and simulators: are they connected correctly to the Test-bed, do they run without errors, are they subscribed to the correct topics, and do they publish to the expected topics, are some of the questions that the admin tool can answer for you. In addition, the admin tool makes sure that all message schemas are available. And when everything is in place, the actual Trial can start. Finally, the admin tool offers a convenient interface to all the other technical Test-bed services, such as the REST services, Topics UI, Schema Registry, Kafka Connect, etc.

From then on, the system administrator only needs to check whether the Test-bed does not experience any issues, like disconnected applications.

## 4.2 Use case: Integration process for a single solution or simulator

Within DRIVER+, a dedicated integration process *for solutions* is described in a separate document, D934.21, [Solution testing procedure](https://www.driver-project.eu/wp-content/uploads/2018/08/DRIVERPLUS_D934.21_Solution-testing-procedure.pdf). This section describes how a developer can proceed to integrate a solution or simulator with the Test-bed reference implementation.

This use-case is executed during the integration of a single solution or simulator into the Test-bed. It does not apply to the testing of multiple applications, and should already have been successfully performed before the application is tested in a larger context with multiple solutions and simulators.

1. Developer starts up the Test-bed and the Admin Tool, or uses a Test-bed available online.
2. Developer selects one of the existing adapters, integrates it into his application, and connects to the Test-bed. Adapters are available in multiple languages, each adapter is published separately as a library or package, and has an example project on GitHub explaining how to use it: [Java](https://github.com/DRIVER-EU/java-test-bed-adapter), [C#](https://github.com/DRIVER-EU/csharp-test-bed-adapter), [JavaScript/TypeScript/Node.js](https://github.com/DRIVER-EU/node-test-bed-adapter), [Python](https://github.com/DRIVER-EU/python-test-bed-adapter) and [REST](https://github.com/DRIVER-EU/test-bed-rest-service). The Admin Tool shows whether the connection is established successfully.
3. Developer defines the input/output [AVRO](http://avro.apache.org/docs/current) message schemas and topics based on the running test-bed. Many popular schemas for CAP, EMSI, MLP, GeoJSON etc. have already been defined in our [AVRO-schemas repository](https://github.com/DRIVER-EU/avro-schemas) and should be re-used if applicable.
   1. In case your message format is not available, the developer needs to create a new one and register it with the Test-bed's schema registry. You can do that manually, or alternatively, the adapter will do this for you. The registration procedure is a bit different for each adapter. Please consult the adapter's documentation.
   2. In case your information is available, but in a format that the application does not support, you can create a gateway service to translate messages in one topic, e.g. MLP, to another message format, e.g. GeoJSON, and consume the latter. See the paragraph on the [Gateway service](#44-gateways-for-translating-messages) below.
4. To receive messages, the developer starts up the [Kafka replay-service](https://github.com/DRIVER-EU/kafka-replay-service) to send messages one-by-one or replay a logged sequence of messages. For more detailed information, see the [Replay service section](#replay-service) below. To log the messages in a topic, you can use the [Kafka-topics-logger](https://github.com/DRIVER-EU/kafka-topics-logger) or the [Kafka topics UI](https://github.com/Landoop/kafka-topics-ui), which is a default part of the Test-bed, to save them. This is, for example, useful when you need to integrate with an application that does not run locally, e.g. when your COP tool needs to consume messages from a simulator that you do not have running locally.

    In the next version of the Test-bed reference implementation, there will also be a message injector application, comparable to [Swagger](http://swagger.io) or [Postman](https://www.getpostman.com), in which you will be able to create your own messages using a friendly user interface. It will use the AVRO schema to automatically create a form for defining your messages.

5. In case the integrated application also sends messages, Developer can use the [Kafka topics UI](https://github.com/Landoop/kafka-topics-ui) in Figure 12 to verify that they were created and published to the Test-bed successfully.
6. When your message uses time, the developer needs to query the adapter to get the local Trial time. The Test-bed's [time-service](#time) manages the Trial time, and you can read more about it below.

### Message Topics UI

The Test-bed includes Landoop's [Kafka topics UI](https://github.com/Landoop/kafka-topics-ui) service (see Figure 13) to inspect all the message topics that exist (default location [http://localhost:3600](http://localhost:3600)). It can be used to inspect whether you have been successful in sending your messages to the Test-bed.

![Screenshot of Landoop's Kafka topics UI, which is part of our test-bed](img/kafka_topics_ui.png)

### Schema Registry

The Test-bed also includes Landoop's [AVRO schema registry](https://github.com/Landoop/schema-registry-ui) service (see Figure 14) to inspect all the available schema's (default location [http://localhost:3601](http://localhost:3601)). As each message topic only has one schema, every message send to a topic needs to comply with that schema too. Also, in case a developer is creating new messages, these schemas must first be added to the schema registry, either manually or via one of the existing adapters.

![Screenshot of Landoop's AVRO schema registry, which is part of our test-bed](img/avro_schema_registry_ui.png)

### REST service

The Test-bed contains a REST service: in case a (legacy) solution is not adaptable, or the solution is developed in a programming language that is not currently supported, it may be necessary to interact with the Test-bed via the [REST service](github.com/DRIVER-EU/test-bed-rest-service). In that case, the REST interface would be installed and run locally, so the solution that connects to it is still clearly visible in the admin tool as a separate client.

### Replay service

Connecting to the Test-bed is needed to share information that you produce, or to consume information from others. While working on integrating your own simulator or solution, however, it is very likely that there are no other simulators or solutions running. When sending messages, you can use the Kafka topics UI to verify that your messages have been delivered. And it is the purpose of the [replay service](https://github.com/DRIVER-EU/kafka-replay-service) (see Figure 15) to present you with messages to consume.

> For example, assume that you as a developer are tasked to integrate a COP solution. It needs to consume the locations of the rescue vehicles, which are normally generated by a simulator. However, it is a commercial simulator that you do not have. In that case, you request the simulator to run a scenario and publish it to the Test-bed. Next, log all the messages to file using the [Kafka-topics-logger](https://github.com/DRIVER-EU/kafka-topics-logger) or Kafka Topics UI. This log file is subsequently sent to the COP solution developer, who can replay it using the replay service, as if the simulator was present.

![Screenshot displaying the Replay service's GUI interface](img/kafka_replay_service.png)

## 4.3 Use case: Pre-trial integration testing

The procedure for testing multiple solutions and simulators before an actual Trial with participants is performed, is similar to the procedure for testing a single application. It is assumed that the single solutions and simulators have already been successfully integrated with the Test-bed, and all required message schemas are defined.

1. system administrator starts up the Test-bed and the Admin Tool, or uses a Test-bed available online. If not done already, all required schema's are registered with the schema registry.
2. system administrator inspects the Admin Tool and verifies that all required solutions and simulators are available and running online without errors.
3. system administrator starts up the Scenario Manager, loads the Trial scenario, and initializes it. The Test-bed's time service updates the fictive Trial time and state, and every application that uses time should reflect that.
4. system administrator runs the Trial scenario, either from the beginning or at another point in time, e.g. where issues were discovered. The time-service will update the fictive time accordingly.
5. system administrator resets the Trial scenario, and replays it, as many times as required to make sure that everything works as expected.

## 4.4 Gateways for translating messages

As a developer, you may be confronted with message formats you need to consume, but do not support natively in your application. In that case, you can either:

- Adapt your application to support these message formats natively.
- Create a gateway service which translates messages from one message format to a format that your application does understand.

To create such a gateway service is simple: you consume messages from one message topic, convert them, and publish them on another topic. The validation services follow the same approach, and several dedicated services are already available within the [DRIVER+ space on GitHub](https://github.com/DRIVER-EU).

> For example, the [Twitter-gateway](https://github.com/DRIVER-EU/twitter-gateway) translates messages from the Test-bed to Twitter, ready to be consumed via Twitter, or the [XVR-GeoJSON-gateway](https://github.com/DRIVER-EU/gateway-xvr-geojson) converts XVR simulator messages to the GeoJSON standard.

## 4.5 Large file service

Kafka and AVRO are ideally suited for smaller-sized messages, i.e. typically not exceeding 1Mb compressed. However, a typical flooding file or a drone video file may well exceed 1Gb of data. Instead, adapters can upload a file to the [large file service](https://github.com/DRIVER-EU/large-file-service) and send out a [Large-data message](https://github.com/DRIVER-EU/avro-schemas/tree/master/core/large-data) sharing its location. Alternatively, the URL may be embedded in a standard document, like a CAP message.

The large file service currently supports two types of messages:

- Public messages: they can be browsed in a folder like structure.
- Private messages: although not secret, they are shared via an obfuscated URL, similar to services like [wetransfer.com](http://wetransfer.com), so you get security through obscurity.

## 4.6 Data services and data sets

Within a Trial, we need to create a virtual environment where we can safely experiment. This virtual environment is created using data, such as maps, census data, height data, power lines, cell towers, hospitals and care providers, etc. As it is a lot of work to create such a rich data environment, the effort should be shared among the Trial owners, solution and simulator providers. Not only to reduce the workload for a specific organisation, but also to make sure that all parties use the same data. Otherwise, a traffic simulator may use a different roadmap than the simulator that provides a 3D environment, and some roads may be blocked by buildings.

In many cases, real-world data is used, optionally enriched with scenario-specific information. Sometimes, a virtual environment is created, based on real-world data but with altered names.

So in order to share all this gathered data, the Test-bed offers two types of services:

- Docker *volume images* to store all this information together, so the data can be easily shared. A Test-bed user can simply pull the volume image from the Docker hub to have all data instantly available
- *Data services*, to share this data with all users, e.g. there is an [MBtiles service](https://github.com/DRIVER-EU/test-bed-mbtiles-service) to offer map images to COP and COP-like tools, or a [WMS service](https://github.com/DRIVER-EU/test-bed-wms-service) that translate Test-bed messages to WMS map layers, which makes the information available to legacy systems too.

**Security** is yet another reason to have these data services and data sets as part of the Test-bed. Not all Trials have open access to the Internet, but they still need access to this kind of data.

## 4.7 Time management {#time}

A Trial typically is not performed in real-time: either because the incident occurs at night, and people prefer to Trial during working hours, because of the limited availability of participants, or because it would simply take too long. An example of the latter is a flooding incident, which can start days before any flooding actually occurs, so you need to compress the scenario to normal working hours.

Within the Test-bed, therefore, the scenario time (a.k.a. Trial time or fictive time) is controlled via the [time service](https://github.com/DRIVER-EU/test-bed-time-service) (see Figure 16) using [two types of messages](https://github.com/DRIVER-EU/avro-schemas/tree/master/core/time): one for controlling the time, and one for informing adapters about the current scenario time.

As a developer, you do not need to interact with these messages directly, since:

- Every adapter has a time interface to get the current scenario time. Even as a solution developer, you should also use this time to timestamp the messages that you send. For example, if inside your message you refer to a particular time, always base it on the scenario time. 
- Every adapter has a state describing the current scenario phase, which you can optionally use during the integration:
  - **Idle:** no scenario has started. The time interface returns the system time (in UTC).
  - **Initialized:** the scenario is ready to be started. All adapters will receive the scenario start time, and can use this to initialize their service. In the near future, adapters have the ability to inform the Test-bed when they are initialized and ready to start.
  - **Running (started or paused):** the scenario time is moving forward, either in real speed or slower/faster than normally. In case the scenario is paused, the current scenario time is still actively being distributed, but does not progress (speed is 0).
  - **Stopped:** The scenario stops, and the simulation time is no longer being updated.

![State diagram of the time service](img/state_diagram_time_service.png)

> **NOTE:** Even when you do not interface with the time messages, you still must use the time interface when you want to publish a message with a timestamp inside. This timestamp should use the current scenario time. Similarly, in case you display the 'actual' time in your user interface, please also use the current scenario time.

### System time versus scenario time

In Trials executed in the past, the operating system (OS) time was also adjusted to match the scenario time. The advantage was that if you would check the time in your status bar, it would display the current scenario time instead of the real time. Although this is straightforward to do, your OS does not like it, as it will generate files in the past or future, and may mess up your system. Especially when the scenario is paused. That is why the current Test-bed does not require you to synchronize your system time to the scenario time.

### A word about HLA and DIS

Within the Modelling & Simulation community, especially for military use, there are two simulation standards, HLA (High Level Architecture) and DIS (Distributed Interactive Simulation), which are the norm. The reasons why we did not use these standards, not even for the CSS, are:

- They are used for connecting simulators to each other, not for connecting solutions to simulators nor solutions to other solutions.
- Their message format is fixed: if you want to send other information, you have to 're-purpose' existing fields, which is not considered a best practice. Also, they have no support for any CM standard.
- Both have a steep learning curve.
- HLA and DIS form a very small community, so it is difficult to hire people with this knowledge, and you typically have to train general software engineers by yourself. Second, it is difficult to find solutions for a particular problem on the Internet.
- HLA and DIS expect everyone to use Java or C++, and there is even less support for the 'newer' programming languages, like C#, JavaScript, Python, etc.
- HLA requires a run-time infrastructure, which is a kind of test-bed: there are two commercial providers that are expensive. Although there is one [open source version](https://github.com/openlvc/portico), it is feature incomplete and not well maintained. Furthermore, even though these versions should be interoperable, they are not, and they cannot be mixed easily.

That is why this Test-bed is using open source software, so it is easy to find:

- Open source tools to support it, or to connect to it, in many programming languages.
- Answers to questions.
- People that can use it.
- A new schema representing your message.
- And there is no financial hurdle preventing adoption.

Even though the Test-bed does not use HLA or DIS internally, there are many simulators that provide a HLA or DIS export, and that can be useful for a Trial. In those cases, a HLA or DIS simulation environment can be created, as is done normally, including a gateway service to bridge the gap with our test-bed: typically, such a gateway has an HLA connector to retrieve information from the HLA/DIS side, and a subset of the information is published in the CSS. And vice versa. Even though this kind of integration if suboptimal, in practice, this is not really noticeable.

## 4.7 Security, or Authorization and Access Control

Security is an integral part of the Test-bed: although in many cases, security may not be required, it can be a show-stopper if it is not there. For example, when sharing critical infrastructure information, or personal data about people, in a certain topic, you want to be sure that only certain adapters, and therefore Solutions, can access that data. That is why access to certain topics may be prohibited.

The security is integrated into the Test-bed's admin tool, and its adapters:

- The admin tool is responsible for creating the security certificates and for authorizing requests to access a topic. More information on the Policy Access Point (PAP) and Policy Decision Point (PDP) is found at the [website](https://github.com/DRIVER-EU/test-bed-security-authorization-service).
- The adapters implement and respect the decisions from the admin tool's security service and cannot access the restricted topics without being allowed explicitly.

## 4.8 Online Test-beds

As mentioned in section 4.3, it is possible to make use of an online test-bed that is hosted in a Cloud setup. This serves as an alternative to hosting a test-bed yourself and is useful for integration testing: whenever two or more parties wish to test their integration via the test-bed, they can request a cloud-hosted test-bed that is available within a few minutes. All parties can then remotely connect to this test-bed, without requiring any prior setup and configuration.

The current Test-bed cloud consists of four servers configured as a [Docker Swarm](https://docs.docker.com/engine/swarm/) which allows various test-bed compositions to be hosted in parallel. Docker swarm allows any Test-bed docker composition (configured manually, or with the [online tool](https://driver-eu.github.io/docker-composer) to run on the Docker Swarm servers. Each Test-bed can make use of one of the ten available host-names for temporary online test-beds: https://tb1.driver-testbed.eu to https://tb10.driver-testbed.eu.

## 4.9 Reverse Proxy for Test-beds

Optionally the Driver Test-bed can be configured to work with a Reverse proxy called [Traefik](https://traefik.io). This has several advantages:

- The Test-bed Services (i.e. Topic UI, Schema UI, etc) are not separately exposed on their own port, but via a relative URL on a shared proxy that can be reached via a configurable hostname. For example instead of exposing the Topics UI at http://hostname:3601 and the Schema UI at http://hostname:3602, they can be exposed at http://hostname/topics-ui/ and http://hostname/schema-ui/ respectively. They will then both share the same HTTP endpoint at http://hostname.
- The shared HTTP endpoint can be secured with an SSL certificate, for example automatically  created via [lets-encrypt](https://letsencrypt.org/). Because all web services in the test-bed use the same reverse proxy, securing this reverse proxy entry point will secure the HTTP traffic for all services behind it without requiring any configuration on the test-bed services.
- Traefik monitors and logs requests so allow for tracing and monitoring health and load on services in the test-bed.

Traefik is built to integrate seamlessly with Docker Swarm. For setting up the Test-bed with Traefik [these](https://docs.traefik.io/user-guide/docker-and-lets-encrypt/) instructions are followed. Traefik runs in a separate container, on a shared Docker network with the Test-bed containers to allow routing of traffic to the Test-bed services. It listens to events from Docker stating that a container is started. Metadata provided on start-up of the container (labels, see [here](https://docs.traefik.io/configuration/backends/docker)) specifies how the service should be exposed by Traefik. For example Traefik can expose the service at a specified hostname (domain or subdomain) and at a specific relative path. Moreover, the URL may be manipulated (for example strip the relative path) to allow compatibility with the services. Whenever a new service is hosted on a specified hostname, Traefik automatically ensures that a valid lets-encrypt certificate is present or requested.

There are two beta Test-bed compositions available for usage with Traefik on the test-bed repository 'traefik' branch:

- A composition for a local test-bed with Traefik at https://github.com/DRIVER-EU/test-bed/tree/treafik/docker/local. This allows running the test-bed on your local machine. The external hostname, Kafka broker port, and Schema Registry port can be specified in the .env file.
- A composition to run in the cloud using Docker swarm at https://github.com/DRIVER-EU/test-bed/tree/treafik/docker/swarm. This is built for running on the TNO hosted cloud servers. The external hostname, Kafka broker port, and Schema Registry port must be specified using ENV variables `TESTBED_HOST`, `BROKER_PORT`, and `SCHEMA_REGISTRY_PORT` respectively.
