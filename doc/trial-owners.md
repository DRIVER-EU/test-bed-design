# 2. A Test-bed for Crisis Management Practitioners

This Chapter discusses the **future** Test-bed functionality that could benefit a practitioner, so although most functionality can already be realized today, not everything is in place yet.

Trial owners will interact with the Test-bed when they want to Trial one or more solutions. Since Trials vary in complexity and scope, not all TGM steps may be required for every Trial. On the one side of the spectrum, the Trial may be more like a simple and inexpensive software **test**, in which a Trial Owner can verify that a Solution has useful features, is user-friendly, looks good, and does not crash. Typically, this would be done with only a few people. For a CM Solution, such a Trial also requires (some components of) the Test-bed, together with a test-scenario. The test-scenario allows you to test the solution in a relevant context.

> For example, if one would test a COP tool as is, it would probably not have any map layers, basically showing an empty map. Using a test-scenario, the COP tool can be tested in the context of a simple incident, perhaps a flooding, have some ambulances driving around, and map layers showing population information, locations of critical infrastructure, etc.

On the other side of the spectrum, a Trial could involve multiple Solutions, many participants and observers, in a more realistic, operational-like scenario, with proper data collection and evaluation along all dimensions in place. In this case, the full TGM should be applied to perform a proper **assessment** and funded conclusions can be drawn. As the latter requires a serious investment in time, people and budget, a Trial Owner may consider to combine the Trial with a planned exercise to reduce the costs of preparing, executing and evaluating a Trial.

For simple as well as complex cases, the Test-bed supports practitioners by providing an environment in which they can easily Trial new solutions and run exercises:

- **Preparation support:** When preparing a Trial, practitioners can use the Scenario Manager tool to create one or more scenarios for the Trial. In addition, they can already document the observations they wish to see using the Observer Support Tool. As part of the preparation, the Test-bed also offers:
  - **Simulation support:** Due to the nature of the crisis management domain, most solutions cannot be properly tested outside an actual crisis situation. As starting a flooding or burning a forest is clearly not an option to test the solutions, the crisis incident needs to be faked or simulated. The same applies to expected reactions of the environment: people panicking, traffic jams, etc. must be simulated too. As most CM simulators cannot simulate realistically all types of incident and reactions, the Test-bed supports multiple simulators so they can collaborate and create the desired fictitious incident.
  - **Solution support:** The new and shiny COP tool or other solution that is being evaluated never acts standalone in a void. Normally, solutions need to exchange information with other (legacy) solutions too, which is also supported by the Test-bed.
  - **Development support:** To connect new solutions and simulators to the Test-bed, the Test-bed provides adapters in several popular languages and several debugging tools and services. Also, to check whether everything is up-and-running smoothly, developers and system operators can use the Admin tool.
- **Execution support:** Scenarios created during the preparation phase can be 'executed', or played, in order to control the Trial. And the scenario time is controllable too.
- **Evaluation support:** As we are testing and evaluating new solutions, the Test-bed provides the After-Action Review tool for capturing and reviewing sent messages and the Observer Support tool to capture observations. In addition, the exchanged information can be logged and processed for further evaluation after the Trial.

In the following sections, a use case (storyline) description of each is provided. The main actor in each story is Monica, a regional crisis manager with a professional background in fire-fighting. One of the challenges she is facing is that she does not always have a good overview of where her people and trucks are during a large-scale fire, and she is looking for a solution. Please also have a look at the animation, which is available online at [https://vimeo.com/299680658](https://vimeo.com/299680658).

<div class="online-only">
<div style="padding:56.25% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/299680658" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>
</div>

## 2.1 Use Case: Evaluating a solution standalone

Monica has heard about an interesting COP solution via the DRIVER+ Portfolio of Solutions. She considers using it to address her problems in fighting fires. After defining the gaps and specifying the requirements, according to the TGM, she continues in an iterative way:

1. Monica, perhaps supported by an IT professional, visits the Test-bed's [composer website](https://driver-eu.github.io/docker-composer) (see Figure 4). _It allows her, or anyone else, to specify the technical components that should make up the Test-bed. It can be compared to buying a car: do you select an air conditioner, cruise control, and a solar screen, or can you live without them._
2. She opens the solution's tab and sees that the COP tool is available for evaluation. _In the future, available solutions could be linked to the Portfolio of Solutions._
3. She selects it in the menu (see Figure 5).
4. She understands that it is difficult to test a solution without any data / scenario, so she visits the data tab and selects a fire-fighting data set situated in the South of France: *It involves a large-scale forest fire, which is rapidly spreading. Ambulances and fire trucks are deployed and driving around. She can also look at census data of the area and a weather report.*

![Test-bed composer's home page](img/test-bed-composer-home.png)

![Test-bed composer: Selecting a solution](img/test-bed-composer-solutions.png)

*From here on forwards, two alternatives are presented. The first one represents the desired situation. The second alternative is already in place.*

### Alternative 1: Cloud scenario

1. As she currently is not interested in other solutions, she opens the menu and clicks on the DEPLOY button. A dialog opens and informs her to wait while her selected Test-bed is started in the cloud.

2. After a brief moment, the Test-bed is running and she gets her own unique link. She visits this website, and is presented with a simple menu: she can start (pause | stop) the fire fighting scenario, and open the website of the COP tool. *In its layer menu, she can turn on the layer which shows the ambulances and fire trucks. There are also options to turn on the fire fighting layer to show the location of vehicles and staff, etc.*

### Alternative 2: Local scenario

1. As she currently is not interested in other solutions, she opens the menu and clicks on the BUILD button. A dialog opens and she can download the *Docker-compose* file to her PC (see Figure 6).

2. Running a simple command, the Test-bed is downloaded and started on her own PC, and she can interact with the Test-bed as described above.

![Test-bed composer: Downloading the docker-compose.yml file](img/test-bed-composer-download.png)

## 2.2 Use Case: Assessing solutions using a Trial

In order to run a Trial, a similar process as described above could be followed. The main difference is that more services and solutions will be added, and more people are involved. In addition, the Test-bed is needed already well-before the final Trial date, since:

- The scenario is created iteratively.
- Not all solutions or simulators are already able to connect to the Test-bed. So even before the first dry-runs, solutions and simulators should be able to test their connection and integration.

In this case, Monica is supported by the local CM expertise centre, which could also be offered via a consultant, a simulator owner, or otherwise, which helps her in setting up the required infrastructure.

1. She contacts one of the local CM expertise centres, and request a Test-bed in the cloud. It is created for her instantly.
2. She contacts the solution provider, and informs him of her Test-bed. This solution owner can contact the CM expertise centre, and with help of its IT staff, are able to connect to Monica's Test-bed, so Monica can have a first look. This connection to the Test-bed is needed, so the Solution can receive information about an fictitious incident, or from other solutions in a more complex Trial.
3. As part of the online Test-bed, she now has access to the Scenario Manager Tool (SMT, see ??? _under development_), which allows her or one of her CM colleagues, to create the outlines of several wildfire scenarios by recording the main flow of events during an incident. The scenario is key in that it translates specific objectives to a storyline so the solutions or people can be put to the test:
4. As the SMT does not create the actual incident itself, she realizes that she needs some simulators to simulate a wildfire, traffic and evacuating civilians. In DRIVER+'s Portfolio of Solutions, she finds several simulators that are compatible with the Test-bed and contacts their owners. The simulation providers, like the solutions provider, are able to connect to Monica's Test-bed too. Alternatively, it may be offered by the CM expertise centre too. Using the Test-bed connection, the simulators can send incident information to the connected Solution, which it can display. In addition, simulators can share information too, so simulated people do not walk happily through the fire!
5. Monica asks one of her colleagues to detail the questions for the observers, which he can document in the Observer Support Tool.
6. During a Trial, Monica can start the scenario. The Scenario Manager Tool instructs everyone when to perform their planned act. For example, the SMT instructs the Observer Support Tool to send the first set of questions to the observers. Monica can also control the scenario time, for example by freezing the time during a break. In case the users of a Solution missed an important test moment, she can also invoke optional storylines or acts, so the Solution and its users get another chance to prove itself.
