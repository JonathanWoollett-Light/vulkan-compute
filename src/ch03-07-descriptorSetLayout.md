## Creating a descriptor set layout

This section refers to:
- `Utility::createDescriptorSetLayout`

> 'Whoa! Wait a second, what even is a descriptor set?'

You may be saying...

Simply: descriptor sets define how shaders access buffers.

Before defining a descriptor set (which defines specific access) we define its layout, this defines less specific things, like how many buffers we have (well, rather how many entries for buffer descriptors we have).

Before jumping straight into this it's worth covering some basic ideas about buffers in GLSL, in affect, buffers are just structures holding some data, for example:

```glsl
layout(binding = 0) buffer Buffer {
    uint x[];
    float y;
    // ...
};
```

They have a specific binding (here defined as `binding = 0`) which corresponds to a specific buffer (or set of buffers). These bindings can also be defined as arrays:

```glsl
layout(binding = 0) buffer Buffer {
    uint x[];
    float y;
    // ...
} buffers[3];
```

Such that there are 3 objects of this structures accessed as `buffers[i]`.

Without specifying it defaults to `buffers[1]`.

In creating our descriptor set layout:
- `VkDescriptorSetLayoutBinding::binding` defines `binding = 0`
- `VkDescriptorSetLayoutBinding::descriptorCount` defines ` buffers[n]`, specifically in this case with `VkDescriptorSetLayoutBinding::descriptorCount=1` it defines it as `buffers[1]`, which means we can write our GLSL without specifying an array size (like the 1st GLSL binding).

```cpp
VkDescriptorSetLayoutBinding binding = {
    // `layout(binding = 0)`
    .binding = 0,
    .descriptorType = VK_DESCRIPTOR_TYPE_STORAGE_BUFFER,
    // Specifies the number buffers of a binding
    //  `layout(binding=0) buffer Buffer { uint x[]; }` or
    //   `layout(binding=0) buffer Buffer { uint x[]; } buffers[1]` would equal 1
    //
    //  `layout(binding=0) buffer Buffer { uint x[]; } buffers[3]` would equal 3,
    //   in affect saying we have 3 buffers of the same format (`buffers[0].x` etc.).
    .descriptorCount = 1,
    .stageFlags = VK_SHADER_STAGE_COMPUTE_BIT
};
// Descriptor set layout options
VkDescriptorSetLayoutCreateInfo descriptorSetLayoutCreateInfo = {
    // `bindingCount` specifies length of `pBindings` array, in this case 1.
    .bindingCount = 1,
    // array of `VkDescriptorSetLayoutBinding`s
    .pBindings = &binding
};

// Create the descriptor set layout. 
VK_CHECK_RESULT(vkCreateDescriptorSetLayout(
    device, &descriptorSetLayoutCreateInfo, nullptr, descriptorSetLayout
));

```