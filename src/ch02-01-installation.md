## Installation

I expect you may have much of what I suggest here already installed, as such you may be able to skip this section.

There are always alternatives, I simply note the way I went about it.

### C++ with [GCC](https://en.wikipedia.org/wiki/GNU_Compiler_Collection) via [MinGW](https://mingw.org)

### [CMake](https://cmake.org/download/)

To make a cross platform project.

Go to [https://cmake.org/download/](https://cmake.org/download/) and download & install the appropriate version of CMake for your system.

### [Vulkan SDK](https://www.lunarg.com/vulkan-sdk/)

To access the Vulkan API.

Go to [https://vulkan.lunarg.com/sdk/home](https://vulkan.lunarg.com/sdk/home) and download & install the appropriate version of the Vulkan SDK for your system.

### [SPIRV-Tools](https://github.com/KhronosGroup/SPIRV-Tools)

To compile GLSL into SPIR-V.

Go to [this GitHub link](https://github.com/KhronosGroup/SPIRV-Tools/blob/master/docs/downloads.md) and download & install the appropriate version for your system.

> Note: This is not required and only recommended as Vulkan SDK often does not have the most recent versions of the SPIRV-Tools.