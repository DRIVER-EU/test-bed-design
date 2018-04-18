# 4. The Test-bed for developers and sysops

A sysops (system operators or system administrators), in the current context, is responsible for installing the Test-bed on their local network, and making sure that all the solution and simulation providers can get access to this network as well. This task is discussed in the use case 'Installing the Test-bed'.

A developer would be tasked with connecting an existing solution or simulator to the Test-bed. Besides the direct coupling, allowing their tools to receive and publish messages, it most likely also involves translating existing messages to their own format. Finally, in case you are connecting a simulator, you also need more detailed information about the time management in the Test-bed.

## 4.1 Use case: Installing the Test-bed

The previous chapter already explained how to setup the Test-bed. More detailed information about how to run the Docker-compose environment can be found [here](https://github.com/DRIVER-EU/test-bed/tree/master/docker). Alternatively, in the near future, use the [GUI](https://driver-eu.github.io/docker-composer).

System administrators are also responsible for setting up the local network, such that all solution and simulator providers have access to the local intranet as well as extranet.

In particular, it should be considered that some providers make heavy use of the network, e.g. to download maps, stream video, or access external computer clusters. If that is the case, consider using a throttling service in your network, so one provider does not claim all the network traffic.

More directly related to the Test-bed, however, is the connection of all solutions and simulators: are they connected correctly to the Test-bed, do they run without errors, are they subscribed to the correct topics, and do they publish to the expected topics, are some of the questions that the admin tool can answer for you. In addition, the admin tool makes sure that all message schemas are available. And when everything is in place, the actual Trial can start. Finally, the admin tool offers a convenient interface to all the other technical Test-bed services, such as the REST services, Topics UI, Schema Registry, Kafka Connect, etc.

From then on, the system administrator only needs to check whether the Test-bed does not experience any issues, like disconnected applications.

## 4.2 Use case: Integration process for a single solution or simulator

Within DRIVER+, a dedicated integration process *for solutions* is described in a separate document, D934.21, "Solution testing procedure". This section describes how to integrate with the Test-bed reference implementation, and also covers simulators and other tools.

This use-case is executed during the integration of a single solution or simulator into the Test-bed. It does not apply to the testing of multiple applications, and should already have been successfully performed before the application is tested in a larger context with multiple solutions and simulators.

1. Developer starts up the Test-bed and the Admin Tool, or uses a Test-bed available online.
2. Developer selects one of the existing adapters, integrates it into his application, and connects to the Test-bed. The Admin Tool shows whether the connection is established successfully. Adapters are available in multiple languages: [Java](https://github.com/DRIVER-EU/java-test-bed-adapter), [C#](https://github.com/DRIVER-EU/csharp-test-bed-adapter), [JavaScript/TypeScript](https://github.com/DRIVER-EU/node-test-bed-adapter) and [REST](https://github.com/DRIVER-EU/test-bed-rest-service). A Python version will be available in the next release.
3. Developer defines the input/output [AVRO](http://avro.apache.org/docs/current) message schemas and topics based on the running test-bed. Many popular schemas for CAP, EMSI, MLP, GeoJSON etc. have already been defined in our [AVRO-schemas repository](https://github.com/DRIVER-EU/avro-schemas) and should be re-used if applicable.
   1. In case your message format is not available, you need to create a new one and register it with the Test-bed's schema registry. You can do that manually, or alternatively, the adapter will do this for you. The registration procedure is a bit different for each adapter.
   2. In case your information is available, but in a format that the application does not support, you can create a gateway service to translate messages in one topic, e.g. MLP, to another message format, e.g. GeoJSON, and consume the latter.
4. Developer starts up the [Kafka replay-service](https://github.com/DRIVER-EU/kafka-replay-service) to send them one-by-one or replay a logged sequence of messages. To log the messages in a topic, you can use the [Kafka-topics-logger](https://github.com/DRIVER-EU/kafka-topics-logger) or the topics UI to save them. This is, for example, useful when you need to integrate with an application that does not run locally, e.g. when your COP tool needs to consume messages from a simulator that you do not have running locally.

    In the next version of the Test-bed reference implementation, there will also be a message injector application, comparable to [Swagger](http://swagger.io) or [Postman](https://www.getpostman.com), in which you will be able to create your own messages using a friendly user interface. It will use the AVRO schema to automatically create a form for defining your messages.

5. In case the integrated application also sends messages, Developer can use the Kafka topics UI to verify that they were created and published to the Test-bed successfully.
6. When your message uses time, you need to query the adapter to get the local Trial time. A first version of the Test-bed's [time-service](https://github.com/DRIVER-EU/test-bed-time-service) to manage the Trial time has just been released, and some adapters already offer an interface to it. So there is no need to query the Test-bed yourself to get these messages. In case no time messages are available, i.e. you are not running a Trial, it returns the local system time. Please also check the time management's state machine in [Section 4.5](#time) below.

Currently, not many solutions have already been integrated with the Test-bed. Now that the Test-bed itself is stable, and adapters are available, the integration of many existing solutions will take place. In the mean time, not all solutions will be able to connect to the Test-bed as described above, and during an actual Trial, the Trial coordinator has to decide if, and to what degree, a solution needs to be integrated.

### Message Topics UI

The Test-bed includes Landoop's [Kafka topics UI](https://github.com/Landoop/kafka-topics-ui) service (see Figure 14) to inspect all the message topics that exist (default location [http://localhost:3600](http://localhost:3600)). It can be used to inspect whether you have been successful in sending your messages to the Test-bed.

![Screenshot of Landoop's Kafka topics UI, which is part of our test-bed](img/kafka_topics_ui.png)

### Schema Registry

The Test-bed also includes Landoop's [AVRO schema registry](https://github.com/Landoop/schema-registry-ui) service (see Figure 15) to inspect all the available schema's (default location [http://localhost:3601](http://localhost:3601)). As each message topic only has one schema, every message send to a topic needs to comply with that schema too. Also, in case a developer is creating new messages, these schemas must first be added to the schema registry, either manually or via an adapter.

![Screenshot of Landoop's AVRO schema registry, which is part of our test-bed](img/avro_schema_registry_ui.png)

### REST service

The Test-bed contains a REST service: in case a (legacy) solution is not adaptable, or the solution is developed in a programming language that is not supported, it may be necessary to interact with the Test-bed via the [REST service](github.com/DRIVER-EU/test-bed-rest-service).

### Replay service

Connecting to the Test-bed is needed to share information that you produce, or consume information from others. While working on integrating your own simulator or solution, however, it is very likely that there are no other simulators or solutions running. When sending messages, you can use the Kafka topics UI to verify that your messages have been delivered. And it is the purpose of the [replay service](https://github.com/DRIVER-EU/kafka-replay-service) (see Figure 16) to present you with messages to consume.

*For example, assume that you as a developer are tasked to integrate a COP solution. It needs to consume the locations of the rescue vehicles, which are normally generated by a simulator. However, it is a commercial simulator that you do not have. In that case, you request the simulator to run a scenario and publish it to the Test-bed. Next, the simulator will log all the messages to file using the [Kafka-topics-logger](https://github.com/DRIVER-EU/kafka-topics-logger). This log file is subsequently sent to the COP solution developer, who can replay it using the replay service, as if it was the simulator was present.*

![Screenshot displaying the Kafka-replay service's Swagger interface](img/kafka_replay_service.png)

## 4.3 Use case: Pre-trial integration testing

The procedure for testing multiple solutions and simulators before an actual Trial with participants is performed, is similar to the procedure for testing a single application. It is assumed that the single solutions and simulators have already been successfully integrated with the Test-bed, and all required message schemas are defined.

1. Sysop starts up the Test-bed and the Admin Tool, or uses a Test-bed available online. If not done already, all required schema's are registered with the schema registry.
2. Sysop inspects the Admin Tool and verifies that all required solutions and simulators are available and running online without errors.
3. Sysop starts up the Scenario Manager (not yet available), loads the Trial scenario, and initializes it. The Test-bed's time service updates the fictive Trial time and state, and every application that uses time should reflect that.
4. Sysop runs the Trial scenario, either from the beginning or at another point in time, e.g. where issues were discovered. The time-service will update the fictive time accordingly.
5. Sysop resets the Trial scenario, and replays it, as many times as required to make sure that everything works as expected.

## 4.4 Gateways for translating messages

As a developer, you may be confronted with message formats you need to consume, but do not support natively in your application. In that case, you can either:
- Adapt your application to support these message formats natively.
- Create a gateway service which translates messages from one message format to a format that your application does understand.

To create such a gateway service is simple: you consume messages from one message topic, convert them, and publish them on another topic. The validation services follow the same approach, and several dedicated services are already available within the [DRIVER+ space on GitHub](https://github.com/DRIVER-EU).

## 4.5 Data services and data sets

Within a Trial, we need to create a virtual environment where we can safely experiment. This virtual environment is created using data, such as maps, census data, height data, power lines, cell towers, hospitals and care providers, etc. As it is a lot of work to create such a rich data environment, the effort should be shared among the Trial owners, solution and simulator providers. Not only to reduce the workload for a specific organisation, but also to make sure that all parties use the same data. Otherwise, a traffic simulator may use a different roadmap than the simulator that provides a 3D environment, and some roads may be blocked by buildings.

In many cases, real-world data is used, optionally enriched with scenario-specific information. Sometimes, a virtual environment is created, based on real-world data but with altered names.

So in order to share all this gathered data, the Test-bed offers two types of services:
- Docker *volume images* to store all this information together, so the data can be easily shared. A Test-bed user can simply pull the volume image from the Docker hub to have all data instantly available
- *Data services*, to share this data with all users, e.g. there is an [MBtiles service](https://github.com/DRIVER-EU/test-bed-mbtiles-service) to offer map images to COP and COP-like tools, or a [WMS service](https://github.com/DRIVER-EU/test-bed-wms-service) that translate Test-bed messages to WMS map layers available to make the information available to legacy systems.

**Security** is yet another reason to have these data services and data sets as part of the Test-bed. Not all Trials have open access to the Internet, but they still need access to this kind of data.

## 4.6 Time management {#time}

A Trial typically is not performed in real-time: either because the incident occurs at night, and people prefer to Trial during working hours, because of the limited availability of participants, or because it would simply take too long. An example of the latter is a flooding incident, which can start days before any flooding actually occurs, so you need to compress the scenario to normal working hours.

Within the Test-bed, therefore, the scenario time (a.k.a. Trial time or fictive time) is controlled via the [time service](https://github.com/DRIVER-EU/test-bed-time-service) (see Figure 17) using [two types of messages](https://github.com/DRIVER-EU/avro-schemas/tree/master/core/time): one for controlling the time, and one for informing adapters about the current scenario time.

As a developer, you do not need to interact with these messages directly, since:
- Every adapter has a time interface to get the current scenario time. Even as a solution developer, you should also use this time to timestamp the messages that you send. For example, if inside your message you refer to a particular time, always base it on the scenario time. 
- Every adapter has a state describing the current scenario phase, which you can optionally use during the integration:
  - Idle: no scenario has started. The time interface returns the system time (in UTC).
  - Initialized: the scenario is ready to be started. All adapters will receive the scenario start time, and can use this to initialize their service. In the near future, adapters have the ability to inform the Test-bed when they are initialized and ready to start.
  - Running (started or paused): the scenario time is moving forward, either in real speed or slower/faster than normally. In case the scenario is paused, the current scenario time is still actively being distributed, but does not progress (speed is 0).
  - Stopped: The scenario stops, and the simulation time is no longer being updated.

![State diagram of the time service](img/state_diagram_time_service.png)

Even though you do not interface with the time messages, you still need to use the time interface when you need to send a message with a timestamp inside. This timestamp should use the current scenario time. Similarly, in case you display the 'actual' time in your user interface, please also use the current scenario time.

### System time versus scenario time

In Trials executed in the past, the operating system (OS) time was also adjusted to match the scenario time. The advantage was that if you would check the time in your status bar, it would display the current scenario time instead of the real time. Although this is straightforward to do, your OS does not like it, as it will generate files in the past or future, and may mess up your system. Especially when the scenario is paused. That's why the current Test-bed does not require you to synchronize your system time to the scenario time.

### A word about HLA and DIS

Within the Modelling & Simulation community, especially for military use, there are two simulation standards, HLA (High Level Architecture) and DIS (Distributed Interactive Simulation), which are the norm. The reasons why we did not use these standards, not even for the CSS, are:
- They are used for connecting simulators to each other, not for connecting solutions to simulators nor solutions to other solutions.
- Their message format is fixed: if you want to send other information, you have to 're-purpose' existing fields, which is not considered a best practice. Also, they have no support for any CM standard.
- Both have a steep learning curve.
- HLA and DIS form a very small community, so it is difficult to hire people with this knowledge, and you typically have to train general software engineers by yourself. Second, it is difficult to find solutions for a particular problem on the Internet.
- HLA and DIS expect everyone to use Java or C++, and there is even less support for the 'newer' programming languages, like C#, JavaScript, Python, etc.
- HLA requires a run-time infrastructure, which is a kind of test-bed: there are two commercial providers that are rather expensive. Although there is one [open source version](https://github.com/openlvc/portico), it is feature incomplete and not well maintained. Although these versions should be interoperable, they are not, and they cannot be mixed.

That's why this Test-bed is using open source software, so it is easy to find:
- Open source tools to support it, or to connect to it, in many programming languages
- Answers to questions
- People that can use it
- A new schema representing your message
- And there is no financial hurdle preventing adoption

Even though the Test-bed does not use HLA or DIS internally, there are many simulators that provide a HLA or DIS export, and that can be useful for a Trial. In those cases, a HLA or DIS simulation environment can be created, as is done normally, including a gateway service to bridge the gap with our test-bed: typically, such a gateway has an HLA connector to retrieve information from the HLA/DIS side, and a subset of the information is published in the CSS. And vice versa. Even though this kind of integration if suboptimal, in practice, this is not really noticeable.
