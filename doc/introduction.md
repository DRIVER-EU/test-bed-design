# 1. Introduction

In the Crisis Management (CM) domain, practitioners need to be prepared for the unexpected: based on past experience and the local incidents they had to deal with, they develop a gut feeling for the things that did not go so well. As with most incidents many lives are involved, they are continuously looking for solutions to improve their response and preparedness.

Within the [DRIVER+ project](http://www.driver-project.eu), a Trial Guidance Methodology (TGM) and tools are developed to help practitioners translate their gut feeling to practical solutions to their problems. The Test-bed is a suite of software tools and services that enables solutions and simulators in the Crisis Management (CM) domain to easily exchange information. This allows end-users in the CM domain to Trial solutions, and see if they address their gaps. Additionally,it can be used to support training exercises as well.

The simplified process to Trial solutions goes like this:

1. PREPARATION PHASE:

   - The gut feeling is matched with one of the well-known crisis management gaps, as experienced by many of their colleagues, e.g. *How to get a real-time and dynamic overview of the position of all personnel?*.
   - These gaps, in turn, are still too generic to address, and are made more specific, leading to so-called 'research questions', e.g. *Which Situational Awareness-increasing solution fits best with our mode of operation?*
   - Existing solutions are reviewed and selected. Some solutions can even be tried out standalone using the Test-bed and an existing mini-scenario.
   - A scenario is developed that puts these solutions to the test. This includes selecting and connecting simulators of a fictive incident, e.g. a flooding simulator, and other simulators to create a realistic environment, e.g. a traffic simulator.
   - Selected solutions and existing legacy systems are connected to the test-bed, so they can receive input from the simulators as well as each other.

2. EXECUTION PHASE:

   The Test-bed is setup, all simulators and solutions are connected, data collection is in place, and a scenario can be run (executed). Before the actual Trial, several dry runs are performed, partially for testing the setup, and partially for training the participants in using the solutions. This phase ends when the Trial is executed and observed.

3. EVALUATION PHASE:

   Based on the recorded observations and collected data during the Trial, the solutions are evaluated with the participants, leading to a good appreciation of how the selected solutions have contributed to solving the problems.

![Test-bed environment](img/test-bed-environment.jpg)

## 1.1 Aim

The Test-bed supports practitioners by providing an environment in which they can easily Trial new solutions and run exercises. This has several implications for the test-bed:

- **Evaluation support:** As we are testing and evaluating new solutions, the Test-bed provides tools for observers and After-Action Review.
- **Simulation support:** Due to the nature of the crisis management domain, most solutions cannot be properly tested outside an actual crisis situation. As starting a flooding or burning a forest is clearly not an option to test the solutions, the crisis incident needs to be faked or simulated. The same applies to expected reactions of the environment: people panicking, traffic jams, etc. must be simulated too.
- **Execution support:** As the solutions are typically tested within an incident scenario, the Test-bed provides tools to create and execute scenario's.
- **Development support:** To connect new solutions and simulators to the test-bed, the Test-bed provides adapters in several popular languages and several debugging tools and services. Also, to check whether everything is up-and-running smoothly, it also has an admin tool.

## 1.2 Scope of the test-bed

This document limits its scope to the core Test-bed design, more specifically, the design of the test-bed's reference implementation, which is an implementation of the [Test-bed specification](https://driver-eu.gitbooks.io/test-bed-specification): it thereby provides an overview of the most important components of the test-bed, how they work together, and how they can be used by different stakeholders.

The CM solutions and simulators that supplement the test-bed, however, are *not* part of the test-bed. The simulators' function is to simulate an incident, and the reactions that may occur in a real world, since we cannot unleash incidents like a flooding and earthquakes on the real world. The solutions are the actual tools that are trialled and evaluated, and measured whether they actually do solve a CM gap. These solutions are fed with the simulator's output, and perhaps the output of other solutions, so end users can observe and evaluate their contribution during a fictive incident.

![Scope of the test-bed](img/test-bed-components-explained.png)

## 1.3 Organisation of the Document

This document is organised as follows:

The [Test-bed description](test-bed-description.md) provides a general overview of the Test-bed reference implementation. It is an easy to read chapter which requires no technical background knowledge, and is aimed at anyone who has to work with the test-bed.

The second chapter, a [Test-bed for Trial owners](Trial-owners.md), is specifically aimed at Trial owners and practitioners, and discusses the functionality the Test-bed offers to them. And also, what it does not offer.

In the chapter, a [Test-bed for developers and sysops](developers.md), the technical side of the Test-bed is explained, specifically, how to manage a Test-bed as a sysop, or how to connect a solution or simulator to it as a developer.

The final chapter offers more details about the [Test-bed design](design.md), and provides an explanation for the main design decisions. The intended audience are developers that need a deeper understanding of the Test-bed and its underlying architecture, e.g. for supplementing the Test-bed or for offering a deeper integration of their solution.

## 1.4 What's new

### Version 1 (2018-03-31)

This is the first version of the Test-bed design document, describing the Test-bed reference implementation v1.
