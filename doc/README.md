# Introduction

The test-bed is a suite of software tools and services that enables solutions and simulators in the Crisis Management (CM) domain to easily exchange information. This allows end-users in the CM domain to trial solutions, and see if they address their gaps. Additionally, it can be used to support training exercises as well.

The test-bed is developed as part of the [DRIVER+ project](http://www.driver-project.eu). Naturally, the project scope is wider than the test-bed's, and also covers a guidance methodology and tool, a Portfolio of Solutions tool, and several international trials. During the project, many CM solutions and simulators are going to be coupled to the test-bed, both from internal partners, as well as external partners that have joined one of the trials.

## Scope

This document limits its scope to the test-bed design, more specifically, the design of the test-bed's reference implementation, which is an implementation of the [test-bed specification](https://driver-eu.gitbooks.io/test-bed-specification): it thereby provides an overview of the most important components of the test-bed, how they work together, and how they can be used by different stakeholders. The CM solutions and simulators that supplement the test-bed, however, are *not* part of the test-bed. The simulators' function is to simulate an incident, and the reactions that may occur in a real world, since we cannot unleash incidents like floodings and earthquakes on the real world. The solutions are the actual tools that are trialed and evaluated, and measured whether they actually do solve a CM gap. These solutions are fed with the simulator's output, and perhaps the output of other solutions, so end users can observe and evaluate their contribution during a fictive incident.

![Scope of the test-bed](img/test-bed-components-explained.png)


## Overview of the chapters in this document

This document is organised as follows:

The [test-bed description](test-bed-description.md) is a general overview of the test-bed reference implementation. It is an easy to read chapter which requires no technical background knowledge, and is aimed at anyone who has to work with the test-bed.

The second chapter, a [test-bed for trial owners](trial-owners.md), is specifically aimed at trial owners and practicioners, and discusses the functionality the test-bed offers to them. And also, what it does not offer.

In the chapter, a [test-bed for developers and sysops](developers.md), the technical side of the test-bed is explained, specifically, how to manage a test-bed as a sysop, or how to connect a solution or simulator to it as a developer.

The final chapter offers more details about the [test-bed design](design.md), and provides an explanation for the main design decisions. The intended audience are developers that need a deeper understanding of the test-bed and its underlying architecture, e.g. for supplementing the test-bed or for offering a deeper integration of their solution.


## What's new

### Version 1 (2018-03-31)
