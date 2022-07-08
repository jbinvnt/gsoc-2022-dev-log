# Illustrating Shader Attribute Issues

Behavior of incremental changes as shown in the [branch](https://github.com/jbinvnt/openscad/blob/illustrate-attribute-issues).

## [3081ebd98](https://github.com/jbinvnt/openscad/commit/3081ebd98)

The shader is used to display the shape in F5 when show edges is enabled, regardless of whether the experimental vertex buffer features are enabled. The debug console displays errors from GL_ERROR_CHECK in a couple places: `shader_attribs_enable` and `disable` in VBORenderer

It is also noted that in the Renderer constructor, the `color_edge` property is `-1` due to optimizations when an attribute is not used by the shader. [line](https://github.com/jbinvnt/openscad/blob/e67f746e6e0bfa86384ec9619f979a860c42bd44/src/glview/Renderer.cc#L117)

F6 behavior is unchanged.

## [1dda06edd](https://github.com/jbinvnt/openscad/commit/1dda06edd)

F6 mode does not render an object. There are also no console errors when running F6. Intermittently, after switching to F6 the axes might disappear, and might not reappear until a switch back to F5 preview and changing the camera view.

## [fc72939c9](https://github.com/jbinvnt/openscad/commit/fc72939c9)

Without enabling the Experimental VxO indexing, there is an assertion failure after calling `createinterleavedVBOs`. This is thrown [here](https://github.com/jbinvnt/openscad/blob/e67f746e6e0bfa86384ec9619f979a860c42bd44/src/glview/VertexArray.cc#L359). The error does not occur when experimental indexing is enabled.

However, when enabling the feature, nothing was then rendered. Also a GL error occurs [here](https://github.com/jbinvnt/openscad/blob/e67f746e6e0bfa86384ec9619f979a860c42bd44/src/glview/VBORenderer.cc#L747). This is what I am currently investigating. It would help to pin down the source of a discrepancy between `elements_size_` of 72 (when using a default test shape of `cube(10);`) vs 36.

## [dfdd6aa76](https://github.com/jbinvnt/openscad/commit/dfdd6aa76)

Same as above.

## [e67f746e6](https://github.com/jbinvnt/openscad/commit/e67f746e6)

Same as above.
