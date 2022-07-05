# First Milestone Report

2022-07-04

## Summary

At the time of writing, it's a few days past my self-imposed milestone of having the ability to use shaders for the F6 render. I've been spending extra time during the past couple days continuing to dig through the `Renderer` classes to find any clues about why nothing I've tried so far has produced the expected result. On July 5 (tomorrow) I have a minor surgery that will require 1-2 days of recovery. So I wanted to upload this report to allow community consideration before I get back to work.

## Process

My original goal was to find the simplest possible insertion point for activating an arbitrary shader (i.e. inserting a call to `glUseProgram`). This way, I thought, without the need for major restructuring I would be able to get started on actual feature work more quickly instead of spending a long time at the beginning without a functioning proof of concept to use as a base. Perhaps it's time to reconsider this approach because it seems to be holding me back, and I've been stuck for a few weeks.

If we still want to keep the originally envisioned bottom-up pathway, then there is a possibility that I try departing from the established derived class `CGALRenderer` and try creating my own class that inherits `VBORenderer` or even `Renderer` directly. However there are many risks to this approach:

* It may not even solve the problem I'm trying to overcome, which could potentially be in one of the lower-down objects used to generate position data (such as `VertexArray`, `polyset`, or `polyhedrons`).
* It risks introducing bugs, leaky abstractions, and/or technical debt. This is especially likely given that I am a new developer on the OpenSCAD project and might be unaware of existing implicit conventions or previously discovered side effects.
* Automated testing might become more difficult if some of my first changes are to a major part of the architecture.

My personal suggestion is to switch to a top-down approach, so that I can start getting useful code written and real commits made in my repository. After recovering from my surgery of course, I could start adding menu items in Qt and the file I/O infrastructure for shader switching. This could be manually tested in preview mode for now, because we know that (somehow) a shader is working with F5, albeit with z-fighting and/or the artifacts introduced by OpenCSG.

We're now almost halfway to the Phase 1 evaluation deadline, and I need to have made significant progress in some aspect before then. I'd appreciate your thoughts and suggestions.


## Technical Report

I have not yet produced and uploaded a complete report of my tracing of the rendering (which would require the time-consuming process of permalinking to specific lines in GitHub, and converting my notes to complete sentences with context). This work may not be a good use of time during GSoC, when the goal is to work on development rather than documentation. After the summer is over, I can refine documentation on my own time so that it might be helpful to the community and my own future development for the project. Some of my notes will need to be removed because they are based on misconceptions, and it's also possible that they will become outdated due to my own changes during GSoC, or changes from other contributors.