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

## Demos



## Detailed Work Completed

All the below features are demonstrated in the above videos.

### Added UI menu items

For selecting shader file location:

> View > Set Shader

For reverting to the default shader:

> View > Use Default Shader
### Included Example Shaders

These shaders were [previously proposed](https://github.com/openscad/openscad/pull/3860). They have been imported to the `shaders/` directory. For clarity their filenames and structure were [modified](#reworked-naming-conventions).

### Modernized Render Process

`CGALRenderer` is the class used for F6 Rendering. It now uses VBOs instead of fixed-function immediate mode calls. This was accomplished by converting the generated polyhedrons to polysets, and adapting the methods used in `OpenCSGRenderer` to render them.

### Dynamically Activate Selected Shaders

The signal from the menu item is connected to Qt slot in the main window. This immediately updates the shader in use by the viewport. There will be [future work](#future-work) in this area to provide consistent behavior when closing/opening OpenSCAD or files within it.

### Retreived Marked Face Data
The boolean `hfaceti->marked` is collected from CGAL and converted into a vertex attribute. The default shader's inputs are constructed to appropriately render cut faces with a different color. Due to the OpenGL specification a `bool` attribute cannot be used so the marked face information is accessed as a `float`. For example, as used in the default shader:

```glsl
if (marked < 0.5) {
    drawColor = color1;
} else {
    drawColor = color3;
}
```

### Control Whether Edges Are Drawn

The default shader was edited to allow user control of whether edges are drawn. Similarly to the marked face data, an `int` uniform must be used to transmit this data because the OpenGL version in use does not currently appear to support the `bool` type.

```glsl
float edgeFactor() {
  if(drawEdges != 1) {
    return 1.0; // only mix in the edge color when edges are shown
  }
  //...
}
```

### Reworked Naming Conventions

Shader file naming conventions are now more consistent and intuitive. The shader name is applied to the parent directory for the GLSL files, and consistently uses `camelCase`. The vertex/fragment shader names were changed from `Preview.{frag,vert}` to `Viewport.{frag,vert}`.

### Added Error Checking

A popup window is shown if a user-specified shader fails to compile. A message with the reason for failure is included. Only shaders that compile successfully are loaded.

## Testing

Bugs and inconsistencies have been fixed so that this pull request passes the full test suite for OpenSCAD.

In order to support all VBO functions when rendering with shaders, the test configuration now enables all of the following experimental options:

* `vertex-object-renderers`
* `vertex-object-renderers-prealloc`
* `vertex-object-renderers-direct`
* `vertex-object-renderers-indexing`

Previously only the first of those options was enabled in the test suite.

## Challenges Encountered

There was a significant ramp-up period in which I needed to familiarize myself with the abstractions already in the codebase for producing and interacting with wrapper classes around data structures for rendering. My notes and exploration during this period could possibly be useful for future work on in-depth documentation of these procedures.

Additionally, grasping the interplay between the set of experimental features in development was a prerequisite for refining my changes to pass the automated test suite in the CI pipeline. The implementation of certain constructs may need to evolve as more features are moved from experimental to stable.

## Last GSoC Commit

I plan to contribute to this feature as a volunteer after GSoC ends. The last commit I made for GSoC is [`4c8e5136f40c3ed4b1bcbb12311139569e982221`](https://github.com/openscad/openscad/pull/4330/commits/4c8e5136f40c3ed4b1bcbb12311139569e982221).

## Future Work

Automated tests should be created to ensure the robustness of shader switching throughout different events while OpenSCAD is running. This includes updating a parameter and re-rendering, opening a new or existing file, and closing the program. Discussions with the community will be undertaken to determine how and when a user's selected shader should persist during these events.