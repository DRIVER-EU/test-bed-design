# 3. Test-bed description

The Test-bed supports practitioners by providing an environment in which they can easily Trial new solutions and run exercises. In this chapter, the main components of the Test-bed are explained (see [Figure 3](./README.md#fig1.2.3)) and is aimed at a fairly technical audience.

## 3.1 Core

The Test-bed must support the exchange of information between solutions, simulators and tools. Information such as the location of an incident, alert messages, or the locations of vehicles. Comparable to people exchanging information via email, chat or twitter, the Test-bed exchanges information using the open-source messaging system [Apache Kafka](https://kafka.apache.org) from the [Apache organisation](http://www.apache.org/).

Since all DRIVER+ solutions are software systems, the Test-bed's support for non-software systems, such as a new process, is limited: Clearly, the Observer Support tool and After-Action Review tool could be used. However, the Test-bed can also provide a suitable environment for exercising a new process, since it offers facilities to create a realistic incident environment. For example, the open source SUMO tool could be used to simulate traffic, and the open source csCOP tool could be used to offer the participants a realistic common operational picture during their Trial. Do note that in this case, the data capturing and analysis would be more primitive compared to technical solutions, in that it would mainly depend on the observations.

### Adapters

Adapters are used to connect solutions and simulators to the Test-bed in order to exchange information. As the Test-bed is based on Apache Kafka, which is used worldwide, there are connectors for most programming languages, so software applications can easily connect to it. While connected to the Test-bed, an application can send and/or receive messages. When you want to *receive* a message, you subscribe to a topic of interest: thereafter, you get all the messages that are published until you end your subscription. Optionally, you can even receive messages that were published in the past, or while you were offline, as Kafka logs all messages for a pre-set time. This is especially useful for mobile clients that are not always connected to a network. To *publish* a message, you just need to send it to a topic of interest and every interested application that has subscribed to this topic receives it instantly.

*For example, to publish a CAP (Common Alerting Protocol) message to all interested parties, you use an adapter to send your CAP message to the 'cap' topic. Every tool that has subscribed to the 'cap' topic will get it right away.*

The default Kafka connectors are lacking certain features that are useful in a Test-bed environment, so several existing connectors have been extended. These extended connectors are called **adapters**, which extend regular Kafka connectors with:

- *Heartbeat signals:* Before you can start a Trial, every solution, simulator and tool needs to be up-and-running. Therefore every adapter transmits a heartbeat signal every 5 seconds to inform others it is online. This is monitored in the [Admin Tool](#22-test-bed-administration-tool).
- *Logging:* Besides being online, it is also important to know that each connected service is running as expected, so each adapter offers the option to log warnings/errors to the Test-bed as well. The logs are displayed in the [Admin Tool](#22-test-bed-administration-tool).
- *Configuration options:* The adapter can be instructed via the Admin tool to subscribe and publish to certain topics, and vice versa, the adapters inform the Admin tool. In addition, this can be configured too externally.
- *Trial Time:* A Trial scenario typically will not run at real-time, so the adapter needs to share the fictive simulation time. In addition, it shares the simulation speed, as we may be running slower or faster than real-time, as well as the simulation state (paused/playing/stopped).
- *Large files:* Although most messages that are shared between applications are small (< 1Mb), some are not. For example, to describe the flooding in an area, or when capturing drone imagery, file sizes may easily exceed 1Gb. For that reason, the Test-bed runs a service to share large files, and adapters facilitate using this service: the large file is uploaded to the service, and the adapter either sends a message when ready, or informs the application. Other applications can subsequently use the returned (URL) address to access the file.
- *Security:* By default, all topics are accessible to all connected adapters, which is fine in most cases. However, sometimes a less liberal access is required, e.g. when sharing the locations of critical infrastructure or network configurations. The latest versions of all adapters, therefore, include a security component which prevents unauthorized users from accessing specified topics.

The Test-bed currently maintains the following adapters: [Java](https://github.com/DRIVER-EU/java-test-bed-adapter), [C#](https://github.com/DRIVER-EU/csharp-test-bed-adapter), [JavaScript/TypeScript/Node.js](https://github.com/DRIVER-EU/node-test-bed-adapter), [Python](https://github.com/DRIVER-EU/python-test-bed-adapter), and a [REST](https://github.com/DRIVER-EU/test-bed-rest-service) adapter, which allows any application to publish and receive messages using basic internet commands.

### Messages

As software applications need to understand the messages they receive, the Test-bed has to assure that every message that is sent complies with the expected format (syntax). For example, when a solution wants to share the location of a vehicle or the value of a sensor, you probably need to capture the vehicle's or sensor's location, as well as its type, speed or sensor value. Then it is important to know that the type will be one out of a list of possibilities, that the location is specified using two numbers, and that the speed or sensor value is a number too.

To capture this information, the common solution is to specify it in a so-called schema. The Test-bed enforces this too, and it uses the open [Apache AVRO](https://avro.apache.org) schema format. All Test-bed schema's are shared publicly in the [DRIVER+ AVRO schema repository](https://github.com/DRIVER-EU/avro-schemas).

**Dealing with standards:** In the CM domain, several standards exists, such as CAP, EDXL or EMSI. They are represented using XML, a textual representation of a message that is easily readable by computers. A recurring problem with all standards, however, is that they rarely represent all the information you would like to share. This often leads to adding new fields, or, even worse, *re-purposing* existing fields. Additionally, not every organisation uses it in the same way. For Trialling new solutions, the Test-bed needs to be flexible and exact, and that's why the Test-bed does support these standards, but converted to the AVRO format. In that way, every connected solution or simulator will exactly know what to expect when reading a message, as new fields can be easily added in a robust way.

### CIS and CSS

The Test-bed distinguishes between a Common Information Space (CIS) and a Common Simulation Space (CSS). The CIS is where _solutions_ exchange information, typically based on current emergency standards such as the Common Alerting Protocol (CAP) messages, or Emergency Data Exchange Language (EDXL) messages. The CSS is for _simulators_ to exchange information. Typically, the CIS will exchange far fewer messages during a Trial, and time synchronisation is simple. In the CSS, however, many more messages are generated, e.g. the location of all vehicles may be updated every second. Simulators often need to be in tight synchronization with each other, e.g. when a flooding simulator floods an area, the traffic simulator should divert its traffic, and in the crowd simulator, people may be drowning.

For simple Trials, the CSS and CIS will run in the same Test-bed. In case performance suffers, it may be necessary to split the CSS and CIS over two Test-beds that are interconnected, a feature that Apache Kafka supports out-of-the-box.

Note, though, that the [adapters](#adapters) can be used to connect to the CIS as well as the CSS, so there is no difference between them.

In rare cases, the CSS may be replaced, or extended, by one of the existing simulation standards such as HLA or DIS, that are especially popular in the Defence sector. Please refer to [Chapter 4](developers.md#a-word-about-hla-and-dis) to learn more about this.

### Gateways and Validation Services

Even when using well defined messages based on [Apache AVRO](https://avro.apache.org), it is certain that not all solutions and simulators speak each other's 'language'. As in Europe, where not everyone is speaking Esperanto or English, there is a need for translators, in the Test-bed we need *gateways* to translate one topic's message to another. Examples are not only translating one message format to another, but for example to translate:

- A message from a simulator sharing the location of all vehicles in the CSS, to a COP tool message in the CIS that only contains the location of its own resources.
- An EDXL Resource Management request from a COP tool in the CIS to a simulator message in the CSS, which in turn sends out an ambulance to the required location.

In order to facilitate Solutions to obtain the needed information from the simulated world, the CSS needs to be connected with the CIS by means of translator applications, residing in the CIS-CSS Gateway (see Figure 7). These translator applications form the bridge between the simulated _truth_, where everything is known in the CSS, and the _perceived truth_ in the CIS by translating relevant changes from the simulated world to messages globally understood in the CIS.

![Conceptual diagram of gateways translating messages back and forth between CIS and CSS](img/conceptual-diagram-of-gateways.png)

> An example of this would be the simulation of a flooding. Imagine a river that has rising water levels due to an increase of rain water. At the river bank, there are several sensors that measure the actual water level. An application is created and connected to current operational systems to send CAP messages regarding the water level in clear categories ranging from LOW to DANGEROUSLY HIGH. A possible solution is assessed on improving decision-making based on the messages outputted by the created sensor application.<br>
In this example, the water level is calculated in a flooding simulator, which connects to the CSS. The gateway listens to the calculated water levels in the CSS, uses it to compute the water levels at the exact sensor locations, and sends out the expected messages to the CIS, similar to the operational application. The decision-support tool, which is connected to the CIS, listens to these formatted messages of the gateway as if it was connected to the actual operational application. This also provides Trial owners the option to experiment with sensor failures, more or fewer sensors, etc.

There are also Solutions that send messages which serve as commands or requests to change the simulated world. Again, a gateway would be used to bridge the CIS and CSS spaces.

> For example, a new dispatch centre Solution allows users to send out emergency services from their dispatch towards the incident location. The Solution would send out a standard resource management message via the CIS. The gateway service picks up the message and translates it to a request for changing the simulation space. The responsible simulator would receive this request via the CSS and simulate an ambulance driving through the simulated world towards the incident location.

**Validation Services** are specific gateways that, as the name suggests, validate a message in more detail, before it is passed on to other systems. For example, if application A is publishing a CAP (Common Alerting Protocol) message for application B, i.e. A → CAP topic → B, the Test-bed will make sure that it complies with the appropriate schema before passing it on. However, there may still be certain aspects in the message that are not completely correct, e.g. the alerting area that is represented as a polygon may not have the same starting and ending point (i.e. it is not _closed_), or the incident location that is represented by two numbers (x, y), may actually be published as (y, x). So during testing, the validation service can 'intercept' messages between A and B and validate them in detail. Only valid messages are passed on, i.e. A → CAP validation topic → CAP topic → B.

## 3.2 Test-bed administration tool

The Test-bed is a collection of services, simulators and solutions running in a distributed network environment, so it is often difficult to understand what is going on exactly. At the same time, the Test-bed needs to be prepared before the Trial can take place. You can compare it to a theatre play, where the stage needs to be prepared, the actors and musicians must be ready, as well as the light and sound engineers. The Test-bed admin tool (see Figure 8) helps you set the stage, monitors both the CIS and the CSS to better understand the status of all services, simulators and Solutions. In particular, it allows you to:

- Publish all AVRO schemas that will be used during a Trial.
- Determine whether all services are up and ready (via the adapter's heartbeat message).
- Make sure that all adapters connect to the expected topics.
- Check the distributed error log to see if any adapter is experiencing problems.
- Setup security, if required.

If all checks are passed, it sends out an 'all-clear' message, and the Trial can start.

![Admin tool](img/admin-tool-screenshot.jpg)

Detailed information:

- [Functional specification](https://driver-eu.gitbooks.io/test-bed-specification/technical-requirements.html)
- [Website](https://github.com/DRIVER-EU/test-bed-admin)

## 3.3 Trialling, Exercising and Scenario Management

Whether designing a **Trial** to evaluate solutions, or an **exercise** to train people, a scenario and, optionally, simulations, are needed to immerge the training audience and to give them the feeling that they are dealing with an actual crisis.

For an exercise, one starts by defining the *training objectives*, *What does the training audience need to learn?*. Next, an appropriate *Mission / Main scenario* is formulated in which these training objectives can be tested and exercised. The mission if further broken down into *storylines*. A Storyline describes a developing situation that will set conditions and provide the Training Audience an opportunity to achieve a specific Training Objective and optional secondary Training Objectives. It often targets a subset of the training audience, e.g. only the fire fighters, and consists of timed events, or so called *injects*. Think of an email to the commander, a 'start flooding' message to a flooding simulator, or instructions to a role-playing actor.

In a **Trial**, although the primary objective is not to train people, but to test and evaluate solutions, a similar procedure is followed. In that case, the *training objectives* are replaced by *research questions*, but the other steps remain the same. More details about the methodology to setup a proper Trial can be found in the [Trial Guidance Methodology](https://www.driver-project.eu/project-public-reports)'s public reports.

### Existing software

The process described above is the typical approach taken by the NATO's Joint Exercise Management Module ([JEMM](http://slideplayer.com/slide/7873364)) (see Figure 9). It is a tool to support live exercises as well as table top exercises, from a few people to battalions. It puts a lot of emphasis on authorization management (*who can do what?*) during the creation of a scenario, and has a limited level of automation. For example, JEMM can connect to Outlook / Exchange Server to automatically create or receive email messages. Sending, though, is still a manual process.

JEMM is only available free of charge to military NATO members, i.e. to use it in a Trial, a military presence is required.

![JEMM exercise script example](img/jemm_exercise_script_example.png)

Alternative commercial solutions exist too, such as [Exonaut](https://www.4cstrategies.com/exonaut-products/training-and-exercise-manager) (see Figure 10). They also aim at a military audience, and follow a similar approach.

![Exonaut timeline example](img/exonaut_example.png)

### Scenario Manager Tool (_under active development_)

A Scenario Manager is an integral part of the Test-bed reference implementation too, since it is not possible to use JEMM or Exonaut directly, as:

- JEMM is only available to NATO members, and can only be used in an exercise when military personnel requests it. This will not always be the case.
- JEMM and Exonaut are aimed at the military community, and the fit with the Crisis Management domain is not optimal.
- JEMM and Exonaut are closed source, so a strong integration with the Test-bed is not possible, as the applications cannot be modified.

The Test-bed's Scenario Manager, then, acts as the *composer* and *conductor* of a classical performance:

- As the *composer*, it defines what each role has to play. For example, what do the simulators or role-players need to do in order to provide a realistic incident and background to the Trial, or it could include sending direct messages to solutions.
- As the *conductor*, it controls *when* each role starts and stops.

It thereby offers its users the following functionality:

- Define the objectives of the Trial.
- Describe an overall scenario that can put these objectives to the test.
- Specific storylines are created to stress the solutions.
- Within each storyline, different acts are created, for example to:
  - Instruct a simulator to start the fire or other incident, or send out social media messages.
  - Send a message to the Observer Support Tool, to switch the set of questions.
  - Inform a role player to conduct a telephone call.

Detailed information:

- [Functional specification](https://driver-eu.gitbooks.io/test-bed-specification/technical-requirements.html)
- [Website](https://github.com/DRIVER-EU/scenario-manager)

## 3.4 Evaluation

Evaluation is needed to verify that the Trial objectives have been achieved. The Test-bed provides two services for this: an Online Observer Support tool and an After-Action Review tool.

### Online Observer Support tool

Based on the specified objectives of the Trial, an observer expects to observe different kinds of behaviour. At the same time, there is little time during a Trial to record behaviour, as the Trial runs on, and that's why the observer tool provides Trial-specific pre-made forms (templates) to quickly create a new observation. For example, *Did you observe role X do Y? Yes/No*. These trial-specific forms are created before the Trial by the observation team manager in the administration panel based on the data collection plan (as described in the Trial Guidance Methodology). Using this panel, specific forms can be assigned to specific observers. The observer can use a tablet, phone or desktop application for his work.

Although the observer tool (see Figure 11) can run standalone, outside of the Test-bed context, there are several benefits when it is connected, since this allows:

- To share observations with Trial staff: they can use this information to steer the Trial in a particular direction.
- The After-Action Review tool can use the observations during the analysis and evaluation.
- The Scenario Manager can inform the observers of major events that are about to occur: so they are warned ahead of time.
- Observation forms can be created dynamically and transmitted to selected observers.

Although the observer tool enables the collection of personal data, research ethics is outside the scope of this technically-oriented document, and is being described in more detail in [D923.21 - Trial guidance methodology and guidance tool specifications (version 1)](https://www.driver-project.eu/wp-content/uploads/2018/08/DRIVERPLUS_D923.11_Functional-Specification-of-the-Test-bed.pdf).

![Observer Support Tool: Left, an overview of available observation templates. Right, one of the observation templates is selected](img/ost_forms_overview.png)

Detailed information:

- [Functional specification](https://driver-eu.gitbooks.io/test-bed-specification/technical-requirements.html)
- [Website](https://github.com/DRIVER-EU/ost)
- [Documentation](https://driver-eu.gitbooks.io/specification-of-the-online-observer-support-tool/)

### After-Action Review tool (_under active development_)

The After-Action Review (AAR) tool collects data during a Trial: messages exchanged during the Trial, observation reports, and screenshots of the solutions during the Trial. Its main purpose is to facilitate the evaluation of the trialled solutions against the predefined objectives, and to help the participants determine how well they functioned. Using a timeline displaying the collected messages, the whole Trial can be analysed visually, and one can quickly jump to a specific point in time in order to inspect a specific message, observation or see the active screenshot.

Detailed information:

- [Functional specification](https://driver-eu.gitbooks.io/test-bed-specification/technical-requirements.html)
- [Website](https://github.com/DRIVER-EU/after-action-review)

## 3.5 Simulation

Much can be said on the subject of simulation, but for the purpose of this chapter, it suffices to provide a brief overview of the Test-bed's relation to simulation.

In the Test-bed, the goal of simulation is to provide a realistic, immersive background for the Trial. Typically, this requires:

- A **simulation of the incident** e.g. a flooding, earthquake or explosion, etc. simulation
- A **simulation of the reactions** to the incident, e.g. people running away or drowning, buildings collapsing, road jams or traffic accidents, etc.
- A **simulation of the perceived world**, i.e. painting a picture of the world to solutions of what they are reasonably expected to see, not what is actually happening. For example, when an area is flooded in the simulation, all simulators know the exact location of the water. So if people are standing knee-deep in the water, or a road is inaccessible due to water, that can be shown and used. However, a COP tool or other solution does not have such a perfect view of what is happening in the world. It does not know where everyone is, nor the exact location of the water level. As long as it has no sensors, cameras, drones, or people informing it, it may well believe that the flooding is in an entirely different location or not happening at all. For example, during a CM exercise, it took the participants quite some time to figure out that the water was actually threatening their own location, and they had not taken the necessary precautions. A well-designed Trial, therefore, needs to think about how they are going to present the simulated world within the Trial.

The Test-bed, therefore, offers support to simulators for creating this realistic and immersive background, by:

- Providing a [time-service and GUI](https://github.com/DRIVER-EU/test-bed-time-service) (see Figure 12): i.e. each adapter knows the scenario time, so simulators and solutions can use this in their user interface and calculations. Think of a clock display, but also when sending an email or CAP message, making sure it uses the correct timestamps.
- The Scenario Manager, as discussed above.

![The Test-bed time-service GUI can be used to start/stop/pause a Trial, as well as set the simulation speed](img/test-bed-time-service.png)

It does not, however, provide these simulators as an integral part of the Test-bed. They are, and shall always remain, external. And even though some simulators will be connected during the project, they are not bound by the open source requirements that the Test-bed has to adhere too. For example:

- XVR connects their 3D crisis management environment, Crisis Media and Resource Manager to the Test-bed, thereby offering their (commercial) services to other parties too.
- DLR connects their open source SUMO (Simulation of Urban Mobility) traffic simulator to the Test-bed, which provides realistic traffic during an incident
- Thales connects their commercial Crowd Simulator SE-STAR to the Test-bed, e.g. providing a realistic simulation of people in need during a crisis.
- TNO connects their critical infrastructure and chain effects simulator to the Test-bed, providing insights into the cascading effects of failing infrastructure.

### A note about Simulators

All simulators have their own data model of how they represent the simulated world. The CSS allows these simulators to agree on a communication form that the simulators understand to create and maintain a jointly simulated world.

The simulators only need to be concerned with maintaining the current state of a given location (including entities and processes present at that location), and do not have to deal with the different kinds of communication types for tools and users to depict that current state.

The CSS allows simulators to only focus on maintaining the current state of the simulated world (i.e. the simulated truth of the incident and the world around it). In order to communicate state changes with other simulators inside the CSS, self-created communication messages are allowed inside this space.