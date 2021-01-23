## Creating a logical device

This section refers to:
- `Utility::createDevice`
- `Utility::getComputeQueueFamilyIndex`

For communicating with a physical device we need a 'logical device', what Vulkan simply names [`VkDevice`](https://www.khronos.org/registry/vulkan/specs/1.2-extensions/man/html/VkDevice.html).

Each logical device uses a specific queue family to which all commands will be submitted.

A queue family can be thought of as a group of threads (queues) with shared functionality.

In our code we:
1. Get the index to the 1st compute supporting queue family on our physical device.
2. Create a logical device.

### Getting compute queue family index

```cpp
uint32_t Utility::getComputeQueueFamilyIndex(VkPhysicalDevice const& physicalDevice) {
    // Gets number of queue families
    uint32_t queueFamilyCount;
    vkGetPhysicalDeviceQueueFamilyProperties(
        physicalDevice, &queueFamilyCount, nullptr
    );

    // Gets queue families
    std::vector<VkQueueFamilyProperties> queueFamilies(queueFamilyCount);
    vkGetPhysicalDeviceQueueFamilyProperties(
        physicalDevice, &queueFamilyCount, queueFamilies.data()
    );

    // Finds 1st queue family which supports compute
    auto itr = std::find_if(queueFamilies.begin(), queueFamilies.end(),
        [](VkQueueFamilyProperties& props) {
            return (props.queueFlags & VK_QUEUE_COMPUTE_BIT);
        }
    );
    if (itr == queueFamilies.end()) {
        throw std::runtime_error("No compute queue family");
    }
    return std::distance(queueFamilies.begin(), itr);
}
```

### Creating a physical device

```cpp
// Find queue family with compute capability.
queueFamilyIndex = getComputeQueueFamilyIndex(physicalDevice);
// Device queue info
VkDeviceQueueCreateInfo queueCreateInfo = {
    .queueFamilyIndex = queueFamilyIndex,
    .queueCount = 1 // create one queue in this family. We don't need more.
};
// Device info
VkDeviceCreateInfo deviceCreateInfo = {
    .queueCreateInfoCount = 1,
    .pQueueCreateInfos = &queueCreateInfo
};

VK_CHECK_RESULT(vkCreateDevice(
    physicalDevice, &deviceCreateInfo, nullptr, &device
)); // create logical device.

// Get handle to queue 0 in `queueFamilyIndex` queue family
vkGetDeviceQueue(device, queueFamilyIndex, 0, &queue);
```