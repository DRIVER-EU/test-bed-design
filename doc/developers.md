# The test-bed for developers and sysops

A sysops (system operators or system administrators), in the current context, is responsible for installing the test-bed on their local network, and making sure that all the solution and simulation providers can get access to this network as well. This task is discussed in the use case 'Installing the test-bed'.

A developer would be tasked with connecting an existing solution or simulator to the test-bed. Besides the direct coupling, allowing their tools to receive and publish messages, it most likely also involves translating existing messages to their own format. Finally, in case you are connecting a simulator, you also need information more detailed information about the time management in the test-bed.

## Use case: Installing the test-bed

The previous chapter already explained how to setup the test-bed. As a system administrator, you are also responsible for setting up the local network, such that all solution and simulator providers have access to the local intranet as well as extranet.

In particular, it should be considered that some providers make heavy use of the network, e.g. to download maps, stream video, or access external computer clusters. If that is the case, consider using a throttling service in your network, so one provider does not claim all the network traffic.

More directly related to the test-bed, however, is the connection of all solutions and simulators: are they connected to the test-bed, run without errors, are they subscribed to the correct topics, and do they publish to the expected topics are some of the questions that the   admin tool can answer for you. In addition, the admin tool makes sure that all message schemas are available, so when everything is in place, the actual trial can start. From then on, the system administrator only needs to check whether the test-bed does not experience any issues.

## Use case: Connecting to the test-bed

## Use case: Translating messages

## Use case: Time management

