## Running a command buffer

This section refers to:
- `Utility::runCommandBuffer`

In our code we:
1. Create a fence.
2. Submit our command buffer.
3. Wait for our fence to trigger.
4. Destroy our fence.

### Fences Vs Semaphores

Fences synchronize command buffers with our application (e.g. waiting for our command buffer to finish execution).

Semaphores synchronize command buffers with each other (e.g. ensuring command buffers execute in specific order).

Here we only need a fence.

### Creating a fence

We use a fence to await our command buffer to complete its execution after we submit it.

A basic fence can be very easily created.

```cpp
// Creates fence (so we can await for command buffer to finish)
VkFence fence;
VkFenceCreateInfo fenceCreateInfo = {};
VK_CHECK_RESULT(vkCreateFence(device, &fenceCreateInfo, nullptr, &fence));
```

### Submitting our command buffer

A little bit more complex we need to specify the command buffers we are submitting and the associated fence that will signal the completion of their execution.

```cpp
// Command buffer submit info
VkSubmitInfo submitInfo = {
    // submit 1 command buffer
    .commandBufferCount = 1,
    // pointer to array of command buffers to submit
    .pCommandBuffers = commandBuffer
};
// Submit command buffer with fence
VK_CHECK_RESULT(vkQueueSubmit(queue, 1, &submitInfo, fence));
```

### Waiting for our fence to trigger

```cpp
// Wait for fence to signal (which it does when command buffer has finished)
VK_CHECK_RESULT(vkWaitForFences(device, 1, &fence, VK_TRUE, 100000000000));
```

### Destroy our fence

```cpp
// Destructs fence
vkDestroyFence(device, fence, nullptr);
```