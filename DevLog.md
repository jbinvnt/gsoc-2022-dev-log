# GSoC 2022 OpenSCAD Dev Log

*Joseph Black*

## Initial Preparation

### April

#### Early/Mid Month

* Introduced myself in IRC
* Discussed possible proposal topics
* Set up a temporary dev environment in a VM
* Wrote, revised, and submitted my [proposal](https://github.com/jbinvnt/gsoc-2022-proposal-public)
* Started investigating #3706

#### Late Month

* Submitted a PR to fix #3706

### May

#### Late Month

* Set up a ZNC bouncer to more easily catch up on past IRC messages
* Configured push notifications in IRC to stay up-to-date on messages for me

### June

#### Early Month

* Started working on configuring my [dev environment](https://github.com/jbinvnt/openscad-docker-vscode) to use VS Code and Docker
* Revisited my proposal to gather questions about beginning feature work

## Coding Period
### June 13

* Got the dev environment working with a temporary but functioning method. GDB was run outside the container and connected to GDBserver inside the container.

### June 14

* Created this Dev Log
* Prepared for my substitutions to the codebase by [investigating](RenderingInvestigation.md) the current state of rendering procedures in OpenSCAD.

### June 15

* Fixed dev env issue where the local backups path used by OpenSCAD was missing

### June 16

* Further debugger tracing of the render processes with different settings

### June 17

* Added more specific code references to the rendering investigation

### June 18

* Captured and recorded the call stack in several places during rendering
* Added to my notes about which functions are called when certain features are enabled

### June 20

* Refined and organized notes made so far
* Prepared first round of questions for mentors/community

### June 22

* Fixed issue in dev environment which wasn't always updating the executable copied out of the container
* Collected feedback about the proper time for activating a user's loaded shader, and discussed existing bugs in this area with the existing codebase

### June 24

* Attempted to collect shader location filenames through stdin piped to the OpenSCAD executable. Concluded that jumping right to the eventual method of choosing them within Qt would be easier
* Investigated IRC notification issue so that I wouldn't miss messages to me

### June 27

* Consolidated my observations about the values of shaderinfo as it is passed through Renderer methods
* Searched through past commits to understand the historical significance of certain additions
* Created branch which removed showedges requirement for using a shader in the F5 preview

### June 28

* Discussed intial logic that leads to a certain Renderer variant being used, and whether the shader is activated
* Activated debug output to more closely examine the potential causes of errors

### June 29

* Inserted additional debug print calls to make it easier to locate what path the rendering is following during tests
* Overhauled the method used in my environment for running GDB, eliminating the server and connecting through the container directly instead

### June 30

* Reworked my dev environment to display a window with debug messages from stdout
* Changed debugging methods in response to mentor suggestions

### July 1-3

* Regrouped my tracing efforts in order to produce meaningful progress on altering the shader used for rendering
* Prepared a report about the current status and plans for reaching the goals of the first milestone

### July 4

* Published the [status report](FirstMilestoneReport.md) about the first milestone

### July 8

* Discussed in more detail the method for how to proceed finding the issues preventing the loaded shader from being active for rendering
* Published a reproduction branch layering an example of my testing method so far

### July 9-12

* Proceeded to track invocation of abstractions above render calls, like the draw method of VertexStates

### July 13

* Started following in more depth the method used in OpenCSGRenderer, discovered where color and vertex attributes are updated before the draw call
* Reported the new progress

### July 14

* Further experiments to follow over a similar method used by OpenCSGRenderer in CGALRenderer
* Coordinated times for further discussion in preparation of the functioning shader switch feature

### July 16

* Achieved a functioning method for using a shader in the F6 render, when the model is made up of polysets
* Added a step converting polyhedrons to polysets, allowing them to be rendered as well

### July 17

* Discussed how to structure the remaining time
* Coordinated testing and feedback on the feature progress so far

### July 18

* Began cataloguing bugs found so far in the imported example shaders from a previous pull request
* Investigated potential limitations with my approach for converting polysets to polyhedrons in order to allow shader rendering
* Cached already-built objects so that subsequent make steps would run faster (change to dev environment)

### July 19

* Started work on a UI menu for selecting the shader to be used
* Further discussion about potentially extending the project deadline
* Confirmed that the polyset conversion is an acceptable solution, negating the need for an extension of the deadline

### July 21

* Small fixes to dev environment versioning issues

### July 22

* Complete work on the initial shader switching menu item. Reported on its operation and published the code for review
* Created a draft pull request for my feature addition so far

### July 24

* Some further testing of shader selection
* Documented an issue where vertex information is not added in subsequent renders after intially rendering with a shader that doesn't utilize as many inputs as others

### July 25

* Responded to feedback about shader path storage and changed to a more stable method

### July 26

* Started marking down additional to-do items to handle the lifetime of the shader selection

### July 27

* Finished recording day-by-day progress throughout the coding period in the dev log
* Additional miscellaneous documentation and reporting ahead of the midterm evaluation deadline