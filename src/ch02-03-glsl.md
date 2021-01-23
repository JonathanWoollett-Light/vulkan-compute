## GLSL

GLSL programs are typically referred to as shaders (and sometimes kernels), when used for compute it it typical to use the `.comp` extension.

The GLSL in [the Hello World project](ch03-00-helloWorld.md):

```glsl
#version 450

layout(local_size_x = 1024, local_size_y = 1, local_size_z = 1) in;

layout(binding = 0) buffer Buffer {
    uint x[]; // u32
};

void main() {
    uint indx = gl_GlobalInvocationID.x;
    x[indx] = indx;
}
```

Before use we must compile our GLSL into SPIR-V `012.comp` → `012.spv`, there are a few ways to do this.

The easiest way I simply copy and paste is by running the BASH command:

```bash
for file in glsl/*.comp; do ./glslc.exe "$file" -o "glsl/$(basename "$file" .comp).spv" --target-env=vulkan1.1; done 
```

Which compiles every `.comp` in the `glsl` sub-folder into a respective `.spv` file using the `glslc.exe` executable in the current directory, using Vulkan 1.1 as the environment.
