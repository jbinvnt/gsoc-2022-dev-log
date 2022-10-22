## About the Project

This project adds support for custom shaders in the 3D viewport. A set of example shaders is included, and users may write their own shaders as well. Graphical features that were historically provided by the old-style rendering method, such as color schemes and differently colored cut faces, are replicated by the new shader-based pipeline. Moreover, now that scene attributes such as cut faces are passed to the shader, it is possible to create limitless custom effects that utilize this scene data.

## Pull Request Structure

All commits related to this project have been consolidated into [this](https://github.com/openscad/openscad/pull/4330/) pull request. The individual fine-grained feature branches were assembled and merged inside my [fork](https://github.com/jbinvnt/openscad).

My original GSoC project proposal may be viewed [here](https://github.com/jbinvnt/gsoc-2022-proposal-public).

| Mentors | Special Thanks |
| --- | -- |
| Torsten Paul @[t-paul](https://github.com/t-paul) | Ryan Colyer @[rcolyer](https://github.com/rcolyer), Hans Loeblich @[thehans](https://github.com/thehans) |

### Topline Summary of Enhancements

* Users can use the UI to select an example shader or one of their own to be used for viewport rendering.
* Marked face data is collected and available to the shader.
* A warning will appear for an invalid shader selection.

## Detailed Work Completed
* Added UI menu item for selecting shader file location
* Imported the [previously proposed](https://github.com/openscad/openscad/pull/3860) example shaders
* Modernized the F6 render process to use VBOs instead of fixed-function immediate mode calls
* Dynamically activate the shader that was selected by the user
* Retreived marked face data from CGAL and constructed shader inputs to appropriately render cut faces with a different color
* Edited shaders to allow user control of whether edges are drawn
* Reworked the shader file naming conventions to be more consistent and intuitive
* Added error checking and a popup window if a user-specified shader fails to compile

## Testing

Bugs and inconsistencies have been fixed so that this pull request passes the full test suite for OpenSCAD.

In order to support all VBO functions when rendering with shaders, the test configuration now enables all of the following experimental options:

* `vertex-object-renderers`
* `vertex-object-renderers-prealloc`
* `vertex-object-renderers-direct`
* `vertex-object-renderers-indexing`

Previously only the first of those options was enabled in the test suite.

## Challenges Encountered

## Last GSoC Commit

I plan to contribute to this feature as a volunteer after GSoC ends. The last commit I made for GSoC is [`4c8e5136f40c3ed4b1bcbb12311139569e982221`](https://github.com/openscad/openscad/pull/4330/commits/4c8e5136f40c3ed4b1bcbb12311139569e982221).