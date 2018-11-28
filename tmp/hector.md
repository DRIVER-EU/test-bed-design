Dear Erik, all,

Find attached my review with some comments on the PDF which are mainly cosmetics and typos.

In my opinion the document is very clear and useful from a technical perspective. It is sometimes a bit less formal (e.g.: including examples) than other deliverables but I think that this enhances readability by a non-technical audience.

About the comment in the review report: <<The deliverable is the pdf version of the online tool. As a standalone document it is not clear which is its target audience, therefore it is sometimes confusing and not very well structured. Some repetitions have been noted throughout the text. The inclusion of the Monica user case is positive but is not well integrated. These remarks should be taken into account for the next versions.>>

In my opinion the problem is that the document is for a technical audience. Section 2 and section 3 are already sections for a somehow technical audience (like I can be). That is why, the reviewers are mentioning repetitions as the same concepts are mentioned on section 4 and 5 although with a totally different scope that the reviewers are not able to understand.

Section 3 is more practitioners oriented but concerning the mention to the Use Case not being well integrated, I think that the reviewers do not expect that Monica as a practitioner will be able to do the work which is described in the use cases in section 3 because the reviewers are not able to understand it as they think that it is too technical (so section 3 which is the one more practitioners oriented is still too technical for them).

I would recommend to have Monica’s use case in section 3 only dealing with the non-technical part and clearly separate what is being done by her (definition of the Trial) and what is being done by the technical team (all the work with the Test-bed composer) which should be included in section 4 (and a cross reference included in section 3).

In fact this is mentioned in the document in section 3: << Typically, setting up the Test-bed for a Trial would not be performed by the Trial owner herself. Most likely, she will be supported by a local system administrator or consultant to help her decide what she needs, and to prepare the technical infrastructure.>>

But then my view is that the reviewers understand what is being described in section 3 as the work to be done by the technical team instead of the one to be done by Monica as a practitioner. In fact, in the next section 4.1 it is mentioned that <<The previous chapter already explained how to setup the Test-bed>>. I think that the technical content should be kept in section 4.

SUMMARIZING

The previous notes were taken while going through the whole document and now at the end I summarize my conclusions and suggest a way forward.

All sections of the document are too technical for the reviewers, they are not able to follow and they do not understand why the same topics are mentioned in different sections because they do not understand that they are mentioned  with a different purpose and scope. Additionally the use case for Monica is also too technical and they do not understand that this could be done by a practitioner (which may be the case).

As food for thought and to address the comments minimizing the required modifications, I would suggest the following (but other options could be considered):

•	I would suggest to start with current section 3 (Monica’s use case) moving current section 2 below.

•	Section 3 should be reshaped to address work that could be done by a practitioner person/team without any technical background and specially having her/him defining her/his needs to be able to run a Trial. ( e.g: I need to do Time management to have my Trial not performed in Real Time). It could be good to mention that after these decisions the technical team will have to use the test bed (which is described in new section 3) in the way documented in section 4. 

•	Then former section 2 (new section 3) would describe the tools that the test-bed is providing to address those needs raised in new section 2 (e.g.: Test-bed is providing the time service to do Time management). In fact, this is already done in former section 2 but as the needs by the practitioners have not been previously mentioned I think that it is hard to follow for the non-technical reviewers. I think that with this approach the section could be almost as it is but just including some references to the previous section.

•	Section 4 will then address how the technical team will have to use the test bed (which is described in new section 3). I think that with this approach also this section could be almost as it is but just including some references to the previous section.

•	Section 5 could be kept as it is but probably even could be moved to an annex as it is showing the main design decisions that were taken and so the linkage between the Test-bed specification and the Reference implementation. 

Probably the names and the introduction of each section and the general introduction could be modified in line with this change.

I think that following this approach not a lot of modifications should be required but just having current section 3 more practitioner/user oriented and restructuring the existing contents. What do you think?

Best regards.
Hector
