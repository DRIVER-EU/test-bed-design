# Annex 2 Change history

This annex documents the changes with respect to previous versions. The returning reader is therefore urged to only review the recent changes.

## Version 2

The changes with respect to the first version are described in the following.

### Executive Summary

- Added a reference to the change history.
- Added link to deliverable D922.21 which is now publicly available.
- Added link to Test-bed animation.
- The Python adapter has become available.
- Added links to the eBook versions (PDF, epub and mobi formats).

### Introduction

- Added a reference to the change history in [section 1.3](introduction.md#13-organisation-of-the-document).
- Clarified the scope and aims of the document, based on the EU reviewers comments.
- Removed a duplicated picture.

### A Test-bed for Crisis Management Practitioners

- Before discussing the Test-bed, first discuss it from the perspective of the Trial owner (chapter 2 and 3 were swapped with respect to the previous version, thereby addressing the reviewers comments).
- Added an description of the Test-bed composer, in order to distinguish it better from the PoS of TGT.
- Embedded Test-bed overview animation (only in the online version).
- Scenario manager use case is expanded.
- Rewrote the use cases, to enhance the role of Monica.
- Removed a duplicated picture.

### Test-bed description

- Added a brief description in [section 2.1](test-bed-description.md#21-core) on how to trial non-technical solutions, i.e. processes.
- Rewrote subsection [Adapters](test-bed-description.md#adapters) and added two new features: security and support for large files.
- [Messages](test-bed-description.md#messages): added link to our public AVRO-schemas repository.
- [CIS and CSS](test-bed-description.md#cis-and-css): wording has slightly changed.
- [Gateways and Validation Services](test-bed-description.md#gateways-and-validation-services): improved the clarity.
- In section 2.2 [Test-bed administration tool](test-bed-description.md#22test-bed-administration-tool): improved the wording.
- Added a new image for the Gateway.
- In section 2.3 [2.3 Trialling, Exercising and Scenario Management](test-bed-description.md#23-trialling-exercising-and-scenario-management):
  - Added link to TGM and functional specification document.
  - Added link to Trial-Management-Tool and AAR website.
  - Added screenshot of time service.
  - Mentioned TNO's critical infrastructure simulator.

### Test-bed for Trial developers & sysops

- New section on Security
- New section on the Large File service
- Embedded Test-bed developer animation (only in the online version).
- Added link to the report "Solution testing procedure".
- Added link to the Python adapter which has become available.
- Added information about two gateways on GitHub (Twitter gateway and XVR-GeoJSON gateway).
- Updated information.
- Updated screenshot of the Replay service.
- Added section on online Test-beds and reverse proxy.

### Test-bed design

- Moved to Annex 3.
- Moved section on large data to Chapter 4.
- Truncated section on the Adapters, and referred to Chapter 3.

## Version 3

The changes with respect to the second version are described in the following.

### Overall changes

- Renamed Scenario Manager to Trial-Management-Tool: the reason for this change was that a Trial may contain several scenarios.

### Introduction

- Added a reference to the change history in [section 1.3](introduction.md#13-organisation-of-the-document).

### A Test-bed for Crisis Management Professionals

- Included a new diagram with the high-level overview of all Trial components (Figure 4).
- Included a remark that the Test-bed is also useful for supporting table top or field exercises, even if this is out-of-scope.
- In Use Case 2.1, added a manual Test-bed creation mode. In practice, Test-beds are mostly created by technical people, and they prefer to work directly with the text-based Docker images files.
- In Alternative 1, Cloud scenario, the actually implemented cloud scenario is added. It turned out that the effort required for the ideal situation was too high, compared to the low effort needed for the current implementation.
- In Alternative 1, Local scenario, the current way-of-working is added.
- Slightly updated Use Case 2.2 to reflect the latest state.

### Test-bed description

- Added a conclusion / roadmap section at the end.
- Added a short description on how the Test-bed can be used for trialling processes and procedures. The previous version was a bit negative about its use, but that vision has changed due to more recent experiences.
- Added an overview of the type of messages that can be exchanged.
- Added direct link to TGM handbook.
- Rewritten the part about the TMT.
- AAR text updated, added images.
- Added two papers (ITEC2019 and SUMO conference)
- Minor textual edits.
- Updated most images.

### The Test-bed for developers and system administrators

- Improved intro, added link to node.js example project.
- Added more detail to use case 4.2.
- Added section on Single-Sign-On as part of section 4.7.
- Rewritten the paragraph 'Dealing with standards' to better explain why we are using AVRO.
- Added a new section: 'Scenario support during a Trial or exercise'.
- Updated the section on JEMM. Added example of the Command Staff Trainer.
- Removed reference to the message injector.
- Renamed Message topics UI to the Kafka topics UI.
- Renamed Schema registry to AVRO schema registry.
- Time service: added a remark that we are always communicating UTC time.
- Time: improved consistency, scenario time is always referred to as trial time.
- HLA: There is nowadays the option to use C#.
- Minor textual edits throughout the chapter.

### Annex 3: Test-bed design

- Minor textual edits throughout the chapter.
