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

#### Out of Scope:

This project will not explicitly implement encryption, credentials, certificates -- the aim is to demonstrate the coordination of work between humans and agents through access controls, and the enforcement of permissions with in teams. 


### Requirements


### Analysis and Design


### Implementation


### Testing and Evaluation


### References



