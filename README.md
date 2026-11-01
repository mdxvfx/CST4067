# CST4067
### Coursework for CST4067 Cloud and Big Data Technologies

This project is submitted as part fulfilment of **MSc Data Science** at Middlesex University 2026-27

### Introduction

Universal Scene Description (USD) is an exchange format developed by Pixar for film and animation productions.  It is designed to allow teams of artists to combine work by publishing 3D models, characters, animations and effects to a common file format which can be assembled into a larger 3D scene or USD `Stage`.  USD files contain 3D objects as `Prims`, organised into Layers which can specify their purpose within the `Stage`.  USD files can reference other USD files as payloads as well as external 3D assets. The final `Stage` is the combined graph of USD layers across all referenced files after their assets and purpose are resolved.

The USD format can be extended by schema which define new attributes and types.  The latest release of USD, 26.11, includes the schema `UsdMediaAuthorshipAPI` which defines attributes for tagging USD files with metadata specifying the author of the file which could be human or generative AI.

This project aims to provide access control and security within a hybrid human / agent film or animation production environment using a USD pipeline so that productions can:

* Check inputs and outputs for authorship
* Reason about GenAI "cleanliness" for productions which allow or disallow certain uses of AI
* Control access to inputs and outputs by agents or humans based on studio set policies around creative control
* Respect the team and third-party artist's licensing of their work for use / non-use with AI

A team could be purely human workers with a no GenAI policy, a purely human team with manual use of approved AI tools (eg alpha matte generation, monocular mocap, concept art that does not appear in final renders), a hybrid team with agents doing prep work (automatic ingest, conform, export of plates), or a hybrid team with a human directing agents to generate final shots.

At all times, the system should be able to compute the level of GenAI usage in the production as well as the allowed operations which might "contaminate" the production with disallowed generative outputs based on the extended schema of the USD files themselves.



### Requirements


There are many reasons why a team would want to have strict control over generative AI within their production.  Among these are:

* Concerns over GenAI exposing them to copy right infringements
* A personal desire for supporting human artists
* Protection of their original Intellectual Property from use as training data

The `UsdMediaAuthorshipAPI` schema recommends the [IPTC Digital Source Type](https://cv.iptc.org/newscodes/digitalsourcetype/) vocabulary for specifying the class of digital assets with respect to their authorship.  The vocabulary covers assets created "manually" by humans using traditional software applications `digitalCreation`, assets created using conventional, analytical algorithms without training data `algorithmicMedia`, through to generative techniques trained on vast data sets of existing art work `trainedAlgorithmicMedia`.

What it *doesn't* provide is a digital signature to ensure the USD file is truly authored by the entity specified in the metadata.

More importantly for this project, the schema recommends defaulting to an assumed value of **non-AI** authorship if the metadata is missing or incomplete.  For the current project, this is problematic because the aim is to prevent GenAI tainting the production if that is the policy of the team.  So, in this system, USD with unknown authorship **must** be treated as if it was GenAI.

For defining and reasoning over Access Control Policies the project will use the `cedarpy` python binding to the [Cedar Policy](https://cedarpolicy.com/en) engine.

Cedar allows **Role Based Access Control** as well as **Attribute Based Access Control**.

The current project will need to explore both approaches.  For instance, the USD documents will carry metadata about their creation which needs to be reasoned over.  In addition, the creators / consumers of these documents could either be other humans or agents so there will be restrictions based on which roles are allowed for humans, agents or both.

#### Assumption

USD doesn't directly support video files which is a crucial part of film and animation production, especially in the context of Generative AI workflows.  To allow video and image sequences to be referenced by the production within USD files, a schema is needed.  An unofficial schema "USD for Shots" has been drafted for this project which accounts for video elements.  This schema is assumed to be part of the scenarios and pipelines analysed in this project.

#### Out of Scope:

This project will not explicitly implement encryption, credentials, certificates -- the aim is to demonstrate the coordination of work between humans and agents through access controls, and the enforcement of permissions with in teams. It is assumed that all humans and agents are trusted within the team, that they always write metadata to the USD and no work goes to external contractors with unknown policies.


### Analysis and Design

**Three Scenarios**

Here are three scenarios illustrating the steps in a production involving hybrid teams as examples of the reasoning steps involved.  They each cover hybrid human / agent pipelines as these are the most relevant and interesting for this project.  Pure human teams which ban GenAI just need to track which human created an asset and how they created it and metadata on its own is enough to monitor this.  Similarly, a heavily agent driven team already allows GenAI with minimal or low restrictions.  Hybrid teams are where the policies need to be most clearly defined and reliable.

Scenario 1.

Hybrid team allows AI for pre-production but *not* for "final pixel" outputs.
This is most similar to the [Netflix Partner Regulations]() for shows allowed on their streaming platform.

Scenario 2.

Scenario 3.

### Implementation


### Testing and Evaluation


### References



