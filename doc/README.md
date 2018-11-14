# Executive Summary

The Test-bed reference implementation lies at the heart of the Trialling environment of the [DRIVER+ project](driver-project.eu) (see Figure 1 or visit the animation online at [https://vimeo.com/299680658](https://vimeo.com/299680658)). It provides an open source technical backbone to perform Trials or exercises in a methodical and structured way by offering practitioners a suite of free *software* tools. This document discusses the Test-bed's usage and design, and therefore it is expected that the reader has at least some technical background. In DRIVER+ deliverable [D923.11](https://driver-eu.gitbooks.io/test-bed-specification) "Functional specification of the Test-bed", the requirements of the Test-bed are documented. These requirements serve as basis for the design of the first release of the DRIVER+ Test-bed reference implementation as described in this deliverable D923.21.

> The current version of this document discusses the **second version** of the Test-bed reference implementation. Therefore, all returning readers are advised to read the [change history](changes.md) carefully, as most parts of this document did not change.

The trial-oriented environment developed in sub-project 92 (Test-bed) of DRIVER+ is conceived and designed to allow systematic testing of solutions in realistic but non-operational contexts (namely, in Trials) to help practitioners in assessing solutions that can drive innovation (changes) before adopting them. See also deliverable [D922.21, "Trial guidance methodology and guidance tool specifications (version 1)](https://www.driver-project.eu/wp-content/uploads/2018/08/DRIVERPLUS_D922.21_Trial-guidance-methodology-and-guidance-tool-specifications.pdf)".

The purpose of conducting Trials in DRIVER+ is to find out if and how some innovative solutions can help resolve the needs of Crisis Management practitioners.

![PTME paradigm applied to DRIVER+](img/pmte_paradigm.png)

The basic problem that the Test-bed reference implementation tries to solve is how to connect different solutions, which solve a particular Crisis Management (CM) gap, to:

- **Each other:** since no single application can solve all CM gaps, they need to work together by sharing information.
- **One or more simulators:** since during a Trial, you cannot start a real incident, there needs to be a way to *simulate* a realistic incident.

The main design decision is to connect solutions to a so-called Common Information Space (CIS), simulators to a Common Simulation Space (CSS), and to have gateway services in between that selectively allow some information to pass between the two spaces (see Figure 2). These spaces can be public or private, and allow applications to write messages to a topic of interest, or read those messages. Although both spaces are comparable in that they share well structured messages (using Apache AVRO) over a popular open source distributed messaging system, Apache Kafka, the separation allows for a better control of the message flow, and for replacing parts with an alternative implementation if needed.

In addition, several adapters are created to connect solutions and simulators to the CIS and CSS: besides allowing users of the Test-bed to choose an adapter in their favourite programming language, and share messages, it also provides a common interface for configuration, heartbeat messages, and security. Currently, adapters in Java, C#, JavaScript/TypeScript, Python and REST are available.

![CIS and CSS](img/test_bed_overview_cis_css.png)

Around this core functionality, additional tools are developed that facilitate the usage of this environment (see Figure 3):

- Administrative tools: Is everyone up-and-running, secure, and connected to the right information topics?
- Evaluation tools: What did we observe during the Trial, and what implications does this have during the After-Action Review.
- Scenario tools: To create an interesting scenario that triggers the participants and solutions in the right way.
- Support tools: For testing and debugging, for creating your personalized Test-bed environment, but also to share common data such as map layers or census data.

![Test-bed reference implementation](img/test-bed-components-explained.png)

The Test-bed will be delivered in 3 versions. Version 1 is intended for use in Trials 1 and 2, and consists of a limited number of components (i.e. Scenario Manager and AAR are not included and other components in first prototype quality). Version 2 (_current version_) is to be used in Trials 3 and 4 and the Final Demo and comes with all components with a quality level surpassing that of a first prototype. It will contain data-sets and basic scenarios that can be used for effectively implementing and testing the Test-bed. The final version, Version 3, should have an even better quality based on experiences gathered from the use of the Test-bed in the executed Trials. 

All versions are already available open-source (under a liberal MIT license) on [github.com/DRIVER-EU](https://github.com/DRIVER-EU). The subsequent updated Test-bed reference implementations are documented in D923.21, D923.22 (this document) and D923.23 - Reference Implementations of the Test-bed (versions 1/2/3 resp.).

This document is a living document, and the latest version is available [online at https://driver-eu.github.io/test-bed-design](https://driver-eu.github.io/test-bed-design) or can be downloaded as
<a href="./ebooks/driver-plus-test-bed-specification.pdf" target="_blank">PDF</a>, <a href="./ebooks/driver-plus-test-bed-specification.epub" target="_blank">epub</a> or <a href="./ebooks/driver-plus-test-bed-specification.mobi" target="_blank">mobi</a> eBook [^1].

[^1]: Since the printed version is automatically generated from the online book, the template is slightly different from a Word-based version. It starts at a high-level with a description of the main components of the Test-bed reference implementation. Next, chapters are dedicated to the main users of this environment, practitioners and developers/ICT administrators. And it concludes with a brief explanation of the main design decisions.
