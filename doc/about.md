# Project information

<table style="border: 2px solid black">
<tr><td style="font-weight: bold; color: white; background: #00497E">Project Acronym:</td><td>DRIVER+</td></tr>
<tr><td style="font-weight: bold; color: white; background: #00497E">Project Full Title:</td><td>Driving Innovation in Crisis Management for European Resilience</td></tr>
<tr><td style="font-weight: bold; color: white; background: #00497E">Grant Agreement:</td><td>607798</td></tr>
<tr><td style="font-weight: bold; color: white; background: #00497E">Project Duration:</td><td>72 months (May 2014 - April 2020)</td></tr>
<tr><td style="font-weight: bold; color: white; background: #00497E">Project Technical Coordinator:</td><td>TNO</td></tr>
<tr><td style="font-weight: bold; color: white; background: #00497E">Contact:</td><td><a href="mailto:coordination@projectdriver.eu">coordination@projectdriver.eu</a></td></tr>
</table>

# Document information

<table style="border: 2px solid black">
  <tr><td style="font-weight: bold; color: white; background: #00497E">Document Status:</td><td>Final</td></tr>
<tr><td style="font-weight: bold; color: white; background: #00497E">Document Title:</td><td>D923.22 Test-bed reference implementation v2</td></tr>
  <tr><td style="font-weight: bold; color: white; background: #00497E">Document Nature:</td><td>Report (R)</td></tr>
  <tr><td style="font-weight: bold; color: white; background: #00497E">Dissemination Level:</td><td>Public (P)</td></tr>
  <tr><td style="font-weight: bold; color: white; background: #00497E">Due Date:</td><td>30/11/2018 </td></tr>
  <tr><td style="font-weight: bold; color: white; background: #00497E">Submission Date:</td><td>30/11/2018</td></tr>
  <tr><td style="font-weight: bold; color: white; background: #00497E">Sub-Project (SP):</td><td>SP92 - Test-bed</td></tr>
  <tr><td style="font-weight: bold; color: white; background: #00497E">Work Package (WP):</td><td>WP923 – Test-bed infrastructure</td></tr>
  <tr><td style="font-weight: bold; color: white; background: #00497E">Document Leader:</td><td>TNO</td></tr>
  <tr><td style="font-weight: bold; color: white; background: #00497E">File Name:</td><td>
  <a href="./ebooks/driver-plus-test-bed-specification.pdf" target="_blank">test-bed-design.pdf</a>, also available as <a href="./ebooks/driver-plus-test-bed-specification.epub" target="_blank">epub</a> or <a href="./ebooks/driver-plus-test-bed-specification.mobi" target="_blank">mobi</a> eBook. The latest version can be found online at <a href="https://driver-eu.github.io/test-bed-design" target="_blank">https://driver-eu.github.io/test-bed-design</a></td></tr>
</table>

<div id="disclaimer" style="font-style: italic; color: grey;">
<strong>DISCLAIMER</strong>

<p>
The opinion stated in this report reflects the opinion of the authors and not the opinion of the European Commission.
All intellectual property rights are owned by the DRIVER+ consortium members and are protected by the applicable laws. Except where otherwise specified, all document contents are: “©DRIVER+ Project - All rights reserved”. Reproduction is not authorised without prior written agreement.
</p>
<p>
The commercial use of any information contained in this document may require a license from the owner of that information.
</p>
<p>
All DRIVER+ consortium members are also committed to publish accurate and up to date information and take the greatest care to do so. However, the DRIVER+ consortium members cannot accept liability for any inaccuracies or omissions nor do they accept liability for any direct, indirect, special, consequential or other losses or damages of any kind arising out of the use of this information.
</p>
</div>

# Revision table

| Issue | Date       | Comment                                 | Author                            |
|-------|------------|-----------------------------------------|-----------------------------------|
| V1.1  | 16/08/2018 | Updating documentation for the second release | Erik Vullings, TNO |
| V1.2  | 15/11/2018 | Review process started | Erik Vullings, TNO |
| V1.3  | 25/11/2018 | Cloud and reverse proxy section added | Pieter Hameete, TNO |
| V1.4  | 26/11/2018 | Reviews from Héctor Naranjo (GMV), Laurent Dubost & Antoine Léger (TCS), Michael Middelhoff (WWU), and Chiara Fonio (JRC) processed | Erik Vullings, TNO |
| V1.5  | 27/11/2018 | Document  review | Angela Schmitt, DLR |
| V1.6  | 28/11/2018 | Preparation of final version | Erik Vullings, TNO |
| V1.7  | 29/11/2018 | Final check and approval for submission | Tim Stelkens-Kobsch, DLR, Quality Manager |
| V1.8  | 29/11/2018 | Final check and approval for submission | Marijn Rijken, TNO, Project Director |
| V2.0  | 30/11/2018 | Submission to the EC | Francisco Gala, ATOS |

<h1 id="driver">The DRIVER+ project</h1>

Current and future challenges due to increasingly severe consequences of natural disasters and terrorist threats require the development and uptake of innovative solutions that are addressing the operational needs of practitioners dealing with Crisis Management. DRIVER+ (Driving Innovation in Crisis Management for European Resilience) is a FP7 Crisis Management demonstration project aiming at improving the way capability development and innovation management is tackled. DRIVER+ has three main objectives:

1. Develop a pan-European Test-bed for Crisis Management capability development:
    - Develop a common guidance methodology and tool (supporting Trials and the gathering of lessons learned.
    - Develop an infrastructure to create relevant environments, for enabling the trialling of new solutions and to explore and share Crisis Management capabilities.
    - Run Trials in order to assess the value of solutions addressing specific needs using guidance and infrastructure.
    - Ensure the sustainability of the pan-European Test-bed.
2. Develop a well-balanced comprehensive Portfolio of Crisis Management Solutions:
    - Facilitate the usage of the Portfolio of Solutions.
    - Ensure the sustainability of the Portfolio of Solutions.
3. Facilitate a shared understanding of Crisis Management across Europe:
    - Establish a common background.
    - Cooperate with external partners in joint Trials.
    - Disseminate project results.

To achieve these objectives, five Subprojects (SPs) have been established. **SP91 Project Management** is devoted to consortium level project management, and it is also in charge of the alignment of DRIVER+ with external initiatives on crisis management for the benefit of DRIVER+ and its stakeholders. In DRIVER+, all activities related to societal impact assessment (from the former SP8 and SP9) are part of SP91 as well. **SP92 Test-bed** will deliver a guidance methodology and guidance tool supporting the design, conduct and analysis of Trials and will develop a reference implementation of the Test-bed. It will also create the scenario simulation capability to support execution of the Trials. **SP93 Solutions** will deliver the Portfolio of Solutions which is a database driven web site that documents all the available DRIVER+ solutions, as well as solutions from external organisations. Adapting solutions to fit the needs addressed in Trials will be done in SP93. **SP94 Trials** will organize four series of Trials as well as the final demo. **SP95 Impact, Engagement and Sustainability**, oversees communication and dissemination, and also addresses issues related to improving sustainability, market aspects of solutions, and standardization.

The DRIVER+ Trials and the Final Demonstration will benefit from the DRIVER+ Test-bed, providing the technological infrastructure, the necessary supporting methodology and adequate support tools to prepare, conduct and evaluate the Trials. All results from the Trials will be stored and made available in the Portfolio of Solutions, being a central platform to present innovative solutions from consortium partners and third parties and to share experiences and best practices with respect to their application. In order to enhance the current European cooperation framework within the Crisis Management domain and to facilitate a shared understanding of Crisis Management across Europe, DRIVER+ will carry out a wide range of activities, whose most important will be to build and structure a dedicated community of practice in crisis management, thereby connecting and fostering the exchange on lessons learnt and best practices between Crisis Management practitioners as well as technological solution providers.
