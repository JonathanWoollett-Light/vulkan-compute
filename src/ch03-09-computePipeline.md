## Creating a compute pipeline

This section refers to:
- `Utility::createComputePipeline`
- `Utility::readShader`

Compute pipelines control how computations are performed.

In our code we:
1. Create a shader
2. Create a pipeline layout
3. Create a compute pipeline

### Shader

We read the length and bytes of our `.spv` file (`vulkan-012/glsl/012.spv`).

```cpp
 // (length, bytes in `uint32_t`s)
auto [fileLength, fileBytes] = readShader(shaderFile);
VkShaderModuleCreateInfo createInfo = {
    .codeSize = fileLength,
    .pCode = fileBytes
};
VK_CHECK_RESULT(
    vkCreateShaderModule(device, &createInfo, nullptr, computeShaderModule
));
```

### Pipeline layout

Pipeline layouts define what types of resources can be accessed by a given pipeline, more specifically the set of resources that can be accessed from shaders of a given pipeline. Created from descriptor set layouts and push constant ranges.

While we can link a pipeline to multiple descriptor sets, we only require the 1 we have for now.

```cpp
VkPipelineLayoutCreateInfo pipelineLayoutCreateInfo = {
    .setLayoutCount = 1, // 1 descriptor set
    .pSetLayouts = descriptorSetLayout // the 1 descriptor set
};
VK_CHECK_RESULT(vkCreatePipelineLayout(
    device, &pipelineLayoutCreateInfo, nullptr, pipelineLayout
));
```

### Pipeline creation

```cpp
// We specify the compute shader stage, and it's entry point(main).
VkPipelineShaderStageCreateInfo shaderStageCreateInfo = {
    .stage = VK_SHADER_STAGE_COMPUTE_BIT, // Shader type
    .module = *computeShaderModule, // Shader module
    .pName = "main" // Shader entry point
};

// Set our pipeline options
VkComputePipelineCreateInfo pipelineCreateInfo = {
    .stage = shaderStageCreateInfo,
    .layout = *pipelineLayout
};

// Create compute pipeline
VK_CHECK_RESULT(vkCreateComputePipelines(
    device, VK_NULL_HANDLE,
    1, &pipelineCreateInfo,
    nullptr, pipeline
));
```