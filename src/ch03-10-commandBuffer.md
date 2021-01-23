## Creating a command buffer

This section refers to:
- `Utility::createCommandBuffer`

It is via command buffers that we submit operations to the device.

In our code we:
1. Create the command pool
2. Allocate the command buffer
3. Dispatch & bind the command buffer

### Command pool

Command pools are objects from which command buffers acquire memory, they can specify some parameters about the usage of command buffers allocated from them via `VkCommandPoolCreateInfo::flags`.

In our case this part is extremely simple:

```cpp
// Creates command pool
VkCommandPoolCreateInfo commandPoolCreateInfo = {
    .sType = VK_STRUCTURE_TYPE_COMMAND_POOL_CREATE_INFO,
    .queueFamilyIndex = queueFamilyIndex // Sets queue family
};
VK_CHECK_RESULT(vkCreateCommandPool(
    device, &commandPoolCreateInfo, nullptr, commandPool
));
```

### Command buffer allocation

Commands buffers record commands that are later submitted to queues where they execute.

There are 2 level of command buffers:
1. `VK_COMMAND_BUFFER_LEVEL_PRIMARY`: Which can be directly submitted and can call secondary command buffers.
2. `VK_COMMAND_BUFFER_LEVEL_PRIMARY`: Which cannot be directly submitted and only called via primary buffers.

For this simple circumstance we want 1 command buffer we can directly submit.

```cpp
// Allocates command buffer
VkCommandBufferAllocateInfo commandBufferAllocateInfo = {
    .sType = VK_STRUCTURE_TYPE_COMMAND_BUFFER_ALLOCATE_INFO,
    .commandPool = *commandPool,  // Pool to allocate from
    .level = VK_COMMAND_BUFFER_LEVEL_PRIMARY,
    .commandBufferCount = 1  // Allocates 1 command buffer. 
};
VK_CHECK_RESULT(vkAllocateCommandBuffers(
    device, &commandBufferAllocateInfo, commandBuffer
));
```

### Command buffer dispatch & binding

To use our command allocated buffers we must bind various resources and specify parameters of operations, this can be thought of as recording these into our command buffers.

Each command buffer must be recorded individually, in our case we only have 1 so it somewhat simplifies the process.

Commands are recorded into the command buffer between `vkBeginCommandBuffer()`
and `vkEndCommandBuffer()`, it cannot be submitted before we finish recording  by calling `vkEndCommandBuffer()`.

```cpp
// Allocated command buffer options
VkCommandBufferBeginInfo beginInfo = {
    .sType = VK_STRUCTURE_TYPE_COMMAND_BUFFER_BEGIN_INFO,
    // Buffer only submitted once
    .flags = VK_COMMAND_BUFFER_USAGE_ONE_TIME_SUBMIT_BIT
};
// Start recording commands
VK_CHECK_RESULT(vkBeginCommandBuffer(*commandBuffer, &beginInfo));

// Binds pipeline (our functions)
vkCmdBindPipeline(*commandBuffer, VK_PIPELINE_BIND_POINT_COMPUTE, pipeline);
// Binds descriptor set (our data)
vkCmdBindDescriptorSets(
    *commandBuffer, VK_PIPELINE_BIND_POINT_COMPUTE, 
    pipelineLayout, 0, 1, &descriptorSet, 0, nullptr
);
```

Set number of workgroups. If we need 1050 x invocations and our workgroup x size is 1024 (`layout(local_size_x = 1024, local_size_y = 1, local_size_z = 1) in;`) we need to dispatch with at least 2 workgroup. Thus with `dims` being the number of invocations we need `[x,y,z]` and `dimLengths` being our workgroup size `[x,y,z]` we set the number of workgroups we need as such:

```cpp
// Sets invocations
vkCmdDispatch(
    *commandBuffer,
    ceil(dims[0] / static_cast<float>(dimLengths[0])),
    ceil(dims[1] / static_cast<float>(dimLengths[1])),
    ceil(dims[2] / static_cast<float>(dimLengths[2]))
);
```

When we finish recording, our buffer is in an executable state and can be submitted.

Recording commands do not report errors, if they
occur, they are reported by the `vkEndCommandBuffer()`.

To finish recording we call `vkEndCommandBuffer()`.

```cpp
// End recording commands
VK_CHECK_RESULT(vkEndCommandBuffer(*commandBuffer));
```