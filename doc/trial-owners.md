# 3. A Test-bed for Trial Owners

Trial owners will interact with the Test-bed when they want to Trial one or more solutions. Since Trials vary in complexity and scope, not all TGM steps may be required for every Trial. On the one side of the spectrum, the Trial may be more like a simple and inexpensive software **test**, in which a Trial Owner can verify that the solution has useful features, is user-friendly, looks good, and doesn’t crash. Typically, this would be done with only a few people. For a CM solution, such a Trial also requires (some components of) the Test-bed, together with a test-scenario. The test-scenario allows you to test the solution in a relevant context.

> For example, if one would test a COP tool as is, it would probably not have any map layers, basically being an empty map. Using a test-scenario, the COP tool can be tested in the context of a simple incident, perhaps a flooding, have some ambulances driving around, and map layers showing population information, locations of critical infrastructure, etc.

On the other side of the spectrum, a Trial could involve multiple solutions, many participants and observers, in a more realistic, operational-like scenario, with proper data collection and evaluation along all dimensions in place. In this case, the full TGM should be applied to perform a proper **assessment** and funded conclusions can be drawn. As the latter requires a serious investment in time, people and budget, a Trial Owner may consider to combine the Trial with a planned exercise to reduce the costs of preparing, executing and evaluating a Trial.

In the following sections, a use case (storyline) description of each is provided. The main actor in each story is Monica, a regional crisis manager with a professional background in fire-fighting. One of the challenges she is facing is that she does now always have a good overview of where her people and trucks are during a large-scale fire, and she is looking for a solution.

## 3.1 Use Case: Evaluating a solution standalone

Monica has heard about an interesting COP solution, *csCOP* via the DRIVER+ Portfolio of Solutions. She considers using it to address her problems

1. Monica visits the Test-bed's [composer website](https://driver-eu.github.io/docker-composer) (see Figure 11).
2. She briefly reads its homepage, which explains her what to do next.
3. She opens the solution's tab and sees that *csCOP* is available for evaluation.
4. She select *csCOP* in the menu (see Figure 12).
5. She understands that it is difficult to test a solution without any data / scenario, so she visits the data tab and selects a fire-fighting data set situated in the South of France: *It involves a forest fire, which is rapidly spreading. Ambulances and fire trucks are deployed and driving around. She can also look at census data of the area and a weather report.*

![Test-bed composer's home page](img/test-bed-composer-home.png)

![Test-bed composer: Selecting a solution](img/test-bed-composer-solutions.png)

*From here on forwards, two alternatives are presented. The first one represents the desired situation. The second alternative is already in place.*

### Alternative 1: Cloud scenario

1. As she currently isn't interested in other solutions, she opens the menu and clicks on the DEPLOY button. A dialog opens and informs her to wait while her selected Test-bed is started in the cloud.

2. After a minute or so, the Test-bed is running and she gets her own unique link. She visits this website, and is presented with a simple menu: she can start (pause | stop) the fire fighting scenario, and open the website of the *csCOP* tool. *In its layer menu, she can turn on the layer which shows the ambulances and fire trucks. There are also options to turn on the fire fighting layer, etc.*

### Alternative 2: Local scenario

1. As she currently isn't interested in other solutions, she opens the menu and clicks on the BUILD button. A dialog opens and she can download the *Docker-compose* file to her PC (see Figure 13).

2. Running a simple command, the Test-bed is downloaded and started on her own PC, and she can interact with the Test-bed as described above.

![Test-bed composer: Downloading the docker-compose.yml file](img/test-bed-composer-download.png)

## 3.2 Use Case: Running a Trial

In order to run a Trial, the same process as described above is followed. The main difference is that more services and solutions will be added, and in many cases, perhaps for security reasons, the Test-bed is run locally on the internal network. Basically, instead of only using the Test-bed core's services, a more complete Test-bed is required, also containing the observer tool, AAR, and Scenario Manager.

In addition, the Test-bed is needed already well-before the final Trial date, since:
- The scenario must be created iteratively.
- Not all solutions or simulators are already able to connect to the Test-bed. So even before the first dry-runs, solutions and simulators should be able to test their connection and integration.

Typically, setting up the Test-bed for a Trial  would not be performed by the Trial owner herself. Most likely, she will be supported by a local system administrator or consultant to help her decide what she needs, and to prepare the technical infrastructure.

When the Test-bed is running, though, the Trial owner has to take responsibility again for defining the scenario in case she has not delegated this task. The scenario is key in that it translates specific objectives to a storyline so the solutions or people can be put to the test.

As the Scenario Manager is not available in the current version of the Test-bed, only a brief outline can be provided. During the preparation, the Scenario Manager acts as the composer:
- The objectives of the Trial are defined.
- An overall scenario is described that can put these objectives to the test.
- Specific storylines are created to stress the solutions.

During a Trial, the scenario is started by the Trial owner. The Scenario Manager, acting as the conductor now, instructs everyone when to perform their planned act. Optionally,she can control the scenario time, for example freezing the time during a break, or to steer the Trial in another direction.
