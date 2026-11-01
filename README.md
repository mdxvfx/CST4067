# CST4067
### Coursework for CST4067 Cloud and Big Data Technologies

This project is submitted as part fulfilment of **MSc Data Science** at Middlesex University 2026-27

### Introduction

Universal Scene Description (USD) is an exchange format developed by Pixar for film, visual effects and animation productions.  It is designed to allow teams of artists to combine work by publishing 3D models, characters, animations and effects to a common file format which can be assembled into a larger 3D scene or USD `Stage`.  USD files contain 3D objects as `Prims`, organised into Layers which can specify their purpose within the `Stage`.  USD files can reference other USD files as payloads as well as external 3D assets. The final `Stage` is the combined graph of USD layers across all referenced files after their assets and purpose are resolved.

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

At the same time, there are potentially many benefits to allowing certain uses of machine learning and generative AI in a film, visual effects or animation pipeline such as the use of agents for pipeline automation tasks.  There is therefore an increasing need for teams to track data flowing through their pipeline to quickly identify authorship.

The `UsdMediaAuthorshipAPI` schema recommends the [IPTC Digital Source Type](https://cv.iptc.org/newscodes/digitalsourcetype/) vocabulary for specifying the class of digital assets with respect to their authorship.  The vocabulary covers assets created "manually" by humans using traditional software applications `digitalCreation`, assets created using conventional, analytical algorithms without training data `algorithmicMedia`, through to generative techniques trained on vast data sets of existing art work `trainedAlgorithmicMedia`.

What it *doesn't* provide is a digital signature to ensure the USD file is truly authored by the entity specified in the metadata.

More importantly for this project, the schema recommends defaulting to an assumed value of **non-AI** authorship if the metadata is missing or incomplete.  For the current project, this is problematic because the aim is to prevent GenAI tainting the production if that is the policy of the team.  So, in this system, USD with unknown authorship **must** be treated as if it was GenAI.

As well as tracking authorship *via* metadata to monitor the level of generative AI in a film or animation production, the project will propose a system for controlling access to media by both humans and agents in the team.

Current studio teams use production tracking tools to manage the development, completion and delivery of shots for a film or animation.  Tools such as [Shotgrid]() and [Kitsu]() already allow teams to store work in progress in a database for asset tracking, task assignment and shot review.  These systems already provide authentication and access control so that team members can only view or edit media assets they have permissions to use.  Access control to shot data within a film, visual effects or animation production is largely a solved problem.  Where this project adds something new is in the access not only to assets but also to tools.  This is particularly important for agents working within a team which may be allowed to perform some automations on media but not others depending on the policy of the team.

For example, teams might allow an agent to automatically catalogue and label footage by analysing frame contents when it uploads it into the database, but not alter its frame rate even if the frame rate doesn't conform to the rest of the footage in the production, preferring a human team member to do the conforming manually.  These sorts of policies would be enforced by limiting access to specific tools in the pipeline.

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

**Scenario 1**

Hybrid team allows AI for pre-production but *not* for "final pixel" outputs.
This is most similar to the [Netflix Partner Regulations]() for shows allowed on their streaming platform.

Item 1 (*Allowed*) use of [Nano Banana]() for generation of character sheets from human created concept art

  - Input: `iptc::digitalCreation` Image
  - Output: `iptc::trainedAlgorithmicMedia` Image
  - Restriction: output must not appear in final pixel outputs
    
Item 2 (*Disallowed*) use of [Tripo AI]() to generate 3D mesh from character sheet

  - Input: `iptc::trainedAlgorithmicMedia` Image
  - Output: `iptc::trainedAlgorithmicMedia` Geometry
  - Restriction: 

Item 3 (*Allowed*) use of Unreal Monocular Metahuman plugin

  - Input: `iptc::digitalCapture` Image Sequence
  - Output: `iptc::trainedAlgorithmicMedia` Skeletal Data
  - Restriction: output is different form of the input

Item 4 (*Disallowed*) use of [NVidia Komodo]() generative animation data

  - Input: `iptc::digitalCreation` Text Prompt
  - Output: `iptc::trainedAlgorithmicMedia` Skeletal Data

The difference between Item 3 and Item 4 is that Item 3 is not predicting the Skeletal Data directly from its training data.  The input for 3 is an original performance captured as video by the team itself and is therefore part of their production work.  The model is trained for human pose estimation from photos of real people.  This is not considered GenAI as it is more similar to traditional Motion Capture which is an `algorithmicMedia` technique.  Item 4 on the other hand is running inference to generate new Skeletal Data statistically from the Skeletal Data in its training set.  It is not conditioned on a human performance that is part of this team's work.  This is a perfect example of the subtle distinctions between algorithmic generation, Machine Learning techniques and Generative AI.  Many teams and productions would consider Item 3 acceptable while Item 4 not be.

**Scenario 2**

Hybrid team allows use of ML techniques to modify their input media and generate temporary previews (pre-visualisation) versions of shots but *not* generate new frames for final output.  All creative decisions must be supervised by a human team member.

Item 5 (*Allowed*) use [LTX 2.5 Alpha Gen]() to generate mattes for film shots

  - Input: principal photography plates from a film shoot `iptc::digitalCapture`
  - Output: alpha channel masks isolating the actors

Item 6 (*Allowed*) use of [IC-Relight LoRA]() to integrate live action footage with CG backgrounds

  - Input: principal photography plates from a film shoot `iptc::digitalCapture`
  - Output: modified plates for compositing with `iptc::digitallyCreated` CG background renderings

Item 7 (*Allowed*) [Nano Banana]() generated storyboard and shot style frame generated from concept art and screenplay breakdown

  - Input: human made concept art `iptc::digitallyCreated`
  - Input: human written screen play `iptc::digitallyCreated`
  - Input: multi camera angle generated shot list `iptc::trainedAlgorithmicMedia` modified by a human artist `iptc::humanEdits`
  - Output: storyboard of style frames for a film sequence
  - Restriction: cannot appear as final output pixels for the film

Item 8 (*Allowed*) [MiniMax H3 restyling](https://comfy.org/hub/workflows/minimax-h3-video-restyling-58a3f7167dba/) preview renderings of shots for reference

  - Input: principal photography plates of grey screen shoots and background reference images `iptc::digitalCapture`
  - Output: generative AI composites of actors with background `iptc::compositeSynthetic`
  - Restriction: 

Item 9 (*Disallowed*) [Layout to Render]() clay render to preview composite of a shot from the storyboard

  - Input A: human made `iptc::digitalCreation` 3D block out scene
  - Input B: Item 5 above created by a human using an approved ML tool
  - Input C: Item 6 above created automatically by an agent working autonomously
  - Input D: Item 7 above created under supervision of a human artist
  - Output: a composite shot of real actors combined with GenAI backgrounds `iptc::compositeSynthetic`
  - Restriction: disallowed because agents are not allowed to make creative decisions affecting final pixels which is failed by Input C

**Scenario 3**

Hybrid team with agents acting autonomously to prepare input and output media that does not form final pixels.

Item 10 (*Allowed*) [Layout to Render]() clay render to preview composite of a shot from the storyboard

  - Input A: human made `iptc::digitalCreation` 3D block out scene
  - Input B: Item 5 above created automatically by an agent working autonomously 
  - Input C: Item 6 above created by a human using an approved ML tool (constitutes final pixels)
  - Input D: Item 7 above created under supervision of a human artist
  - Output: a composite shot of real actors combined with GenAI backgrounds `iptc::compositeSynthetic`
  - Restriction: allowed because agents can work autonomously on work which does not generate final pixels

Item 11 (*Disallowed*) a video edit of all shots which appear in the storyboard (Item 7) generated as above for Item 10 but edited together automatically by an agent

  - Input A: a sequence of shots as generated by Item 10 above, one for each image in a storyboard
  - Output: an edited video sequence made from those shots
  - Restriction: disallowed because video editing requires creative control by a human


The difference between Item 10 and Item 9 above is that Item 9 includes work which has been modified by an autonomous agent *and* could be visible as part of the final look of the work.  Specifically, Relighting the principal photography changes the look of the actors.  Even though Item 9 is just a preview version of the shot and should not be part of the final film or animation, the alteration of the actor's appearance is a creative decision only allowed for human members of the team.  Item 10 also involves agents autonomously working with the same principal photography (the footage of the actors), however it is creating masks and mattes for isolating the actor from the background (think automatic green screen process).  While this does affect the quality of the final composite, it is not changing the fundamental look of the actor or their performance by generating final pixels which is the policy set in scenario 2 and 3.

**Roles and Access**

As can be seen from the scenarios above, there are many interactions and steps in a hybrid production pipeline where either humans or agents consume input media and generate outputs for use down stream by other workers.  One problem this project addresses is how to define roles

### Implementation


### Testing and Evaluation


### References



