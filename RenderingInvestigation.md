# OpenSCAD Render Process Research

## Core Question

My initial conception was that if the `ExperimentalVxORenderers` was not activated (the default), then the old immediate-mode calls would be used for the preview (F5). Otherwise, the modern programmable pipeline would be used (which would be the only way to allow loading shaders from files). From my trace, which is explained in a bit of detail below, I found that the first part of this conception seems to be true. As I knew from the initial discussions back when I was writing my GSoC proposal, there is an issue preventing those shaders from being used unless "show edges" is enabled. I intentionally did not dig into this aspect too deeply yet, but the shader compiled from loading those files is named `edgeshader_prog` as shown [here](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/Renderer.cc#L82).

### Rendering Modes

What's strange is that the shader loaded from these files is used even when `ExperimentalVxORenderers` is turned off. ~~I don't know why this is~~ I investigated this by placing a breakpoint ~~on the colorscheme function~~ ~~`Renderer` constructor~~ [inside](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L376) the `renderCSGProducts` function. And I think that this revealed my initial misconception.

#### The Section with Show Edges and the Shader

[Here](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L391) is where the compiled shader is loaded, only if `showedges == true`.

The `showedges` is obtained from `shaderinfo`, which defaults to `nullptr` which is how it [appears](#default-cube-call-stack) to be called. I still need to investigate with breakpoints to determine what set of inputs reach the branch in which `glUseProgram` is executed.

## Fixed-Function Immediate Mode Preview

These observations were made running OpenSCAD commit [6aae796](https://github.com/openscad/openscad/commit/6aae7963498675dfb5bedaaef2cb8e56bdcddcab), *without* enabling the `ExperimentalVxORenderers` feature (which in the GUI, is called `vertex-object-renderers`). I placed a breakpoint [here](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/Renderer.cc#L301) because this is where I saw `glBegin` immediate mode (fixed-function) OpenGL calls being used. [Note made in post]().

As shown in the [class diagram](https://github.com/openscad/openscad/blob/master/doc/OpenSCAD-classes.pdf), the `Renderer` class is the base class for the three other types of renderers. Upon creating a new OpenSCAD file with all the default options, adding a single cube, and displaying the preview, `Renderer::render_surface` is run. It is run again also any time the camera is moved. Below is a simplified version of the call stack at the breakpoint after orbiting with the mouse causes an update.

### Default Cube Call Stack

Single Cube, `ENABLE_OPENCSG` on, `ExperimentalVxORenderers` off

[`Renderer::render_surface()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/Renderer.cc#L301)  
[`OpenCSGRenderer::renderCSGProducts()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L424)  
[`OpenCSGRenderer::draw()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L105)  
[`GLView::paintGL()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/GLView.cc#L177)  
[`QGLView::paintGL()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/gui/QGLView.cc#L158)  
[`OpenSCADApp::notify()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/gui/OpenSCADApp.cc#L57)  
[`MainWindow::event()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/gui/MainWindow.cc#L1725)  
[`OpenSCADApp::notify()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/gui/OpenSCADApp.cc#L57)  

## OpenCSG Rendering

Reading the Usage section of the [OpenCSG website](https://opencsg.org) is helpful for understanding why the library is included and extended in OpenSCAD the way it is. The "Usage" section of this page explains how OpenCSG is used by subclassing the `Primitive` abstract class and overriding its `render()` function. This happens [here](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L45), but note that this line is not hit if there is only one object which means that OpenCSG is [not used](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L385). In the case of multiple objects, the call stack leading up to `render_surface` looks [like this](#multi-object-call-stack).

### Multi-Object Call Stack

[`Renderer::render_surface()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/Renderer.cc#L301)  
[`OpenCSGPrim::render()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L49)  
[`OpenCSGRenderer::renderCSGProducts()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L386)  
[`OpenCSGRenderer::draw()`](https://github.com/openscad/openscad/blob/6aae7963498675dfb5bedaaef2cb8e56bdcddcab/src/glview/preview/OpenCSGRenderer.cc#L105)  
...

This shows how the `OpenCSGPrim` which extends the `Primitive` class, gets its `render` method called.

### The ENABLE_OPENCSG Option

I have not yet compiled OpenSCAD after turning this option off. I need to look more at `CMakeLists.txt` and the rest of the make infrastructure, unless I can safely assume that `ENABLE_OPENCSG` will always be on.

## Experimental "VxO" Preview

The experimental new renderer was added in a [Pull Request](https://github.com/openscad/openscad/pull/1935). It might not be relevant for the immediate task at hand, but it would be helpful to eventually hear about the community's future plans for stable status of this feature.

Some shaders to test with the `ExperimentalVxORenderers` feature are [here](https://github.com/openscad/openscad/pull/3860). The easiest way to use them for now is to manually replace the contents of `Preview.frag` and `Preview.vert`.

I tested a simple cube-sphere difference with the checkerboard shader. Strangely, it works in preview if show edges is enabled regardless of the status of ExperimentalVxORenderers, even though I showed the fixed-function pipeline being used for the preview by default.
