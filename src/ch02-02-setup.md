## Setup

> Note: This is written for specifically using Visual Studio code, you need not use this editor.

You may already be familiar with what I cover in this section.

### Boilerplate

To begin [clone this](https://github.com/JonathanWoollett-Light/vulkan-boilerplate) and edit `.gitignore` for your specific case.

### Visual Studio Code

Useful extensions:

- GLSL Lint
- C/C++
- CMake Tools
- CMake

On opening the boilerplate project you may be prompted to select a kit to use, if you do not select by this prompt there should be a section in the bottom bar saying 'No Kit Selected' which you must click and select a kit from the given list (you may use GCC).

To confirm setup has succeeded run ctest (this can be done via the ctest button on the bottom). 