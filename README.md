# CST4067
### Coursework for CST4067 Cloud and Big Data Technologies

This project is submitted as part fulfilment of **MSc Data Science** at Middlesex University 2026-27

### Introduction

Universal Scene Description (USD) is an exchange format developed by Pixar for film and animation productions.


This project aims to provide access control and security within a hybrid human / agent film or animation production environment so that productions can:

* Check inputs and outputs for authorship
* Reason about GenAI "cleanliness" for productions which allow or disallow certain uses of AI
* Control access to inputs and outputs by agents or humans based on studio set policies around creative control
* Respect the team and third-party artist's licensing of their work for use / non-use with AI

A team could be purely human workers with a no GenAI policy, a purely human team with manual use of approved AI tools (eg alpha matte generation, monocular mocap, concept art that does not appear in final renders), a hybrid team with agents doing prep work (automatic ingest, conform, export of plates), or a hybrid team with a human directing agents to generate final shots.

At all times, the system should be able to compute the level of GenAI usage in the production as well as the allowed operations which might "contaminate" the production with disallowed generative outputs based on the extended schema of the USD files themselves.

#### Out of Scope:

No need to explicitly implement encryption, credentials, certificates -- the aim is to demonstrate the coordination of work and permissions with in teams. 


