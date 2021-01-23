## Creating a buffer

This section refers to:
- `Utility::createBuffer`
- `Utility::findMemoryType`

For a shader to interact with data we need something to hold data, for this we use buffers, they are generic collections, much like an array.

Defining our buffer and its memory requires more quite a bit more than what you may expect of effectively instantiating an array.

In our code we:
1. Create a buffer
2. Allocate & bind memory

### Creating a buffer

```cpp
// Buffer info
VkBufferCreateInfo bufferCreateInfo = {
    // buffer size in bytes.
    .size = sizeof(float)*size,
    // buffer is used as a storage buffer (and is thus accessible in a shader).
    .usage = VK_BUFFER_USAGE_STORAGE_BUFFER_BIT,
    // buffer is exclusive to a single queue family at a time. 
    .sharingMode = VK_SHARING_MODE_EXCLUSIVE
};

// Constructs buffer
VK_CHECK_RESULT(vkCreateBuffer(device, &bufferCreateInfo, nullptr, buffer));
```

### Allocating & binding memory

We get the memory requirements of our buffer and set the size of the memory to allocate.

```cpp
// Gets buffer memory size and offset
VkMemoryRequirements memoryRequirements;
vkGetBufferMemoryRequirements(device, *buffer, &memoryRequirements);

// Memory info
VkMemoryAllocateInfo allocateInfo = {
    .allocationSize = memoryRequirements.size  // Size in bytes
};
```

To get the type of memory to allocate we loop through available memory until we find memory of our required type and desired properties.

```cpp
allocateInfo.memoryTypeIndex = findMemoryType(
    physicalDevice,
    // Specifies memory types supported for the buffer
    memoryRequirements.memoryTypeBits,
    // Sets memory must have the properties:
    //  `VK_MEMORY_PROPERTY_HOST_COHERENT_BIT` Easily view
    //  `VK_MEMORY_PROPERTY_HOST_VISIBLE_BIT` Read from GPU to CPU
    VK_MEMORY_PROPERTY_HOST_COHERENT_BIT | VK_MEMORY_PROPERTY_HOST_VISIBLE_BIT
);
```

- `memoryRequirements.memoryTypeBits` specifies the memory types our buffer supports and thus the memory we choose must be within this.
- `VK_MEMORY_PROPERTY_HOST_COHERENT_BIT | VK_MEMORY_PROPERTY_HOST_VISIBLE_BIT` specifies properties we require in the memory and thus the memory we choose must include these properties.

```cpp
uint32_t Utility::findMemoryType(
    VkPhysicalDevice const& physicalDevice, 
    uint32_t const memoryTypeBits, 
    VkMemoryPropertyFlags const properties
) {
    VkPhysicalDeviceMemoryProperties memoryProperties;
    vkGetPhysicalDeviceMemoryProperties(physicalDevice, &memoryProperties);

   // Iterate through memory types available on our physical device
    for (uint32_t i = 0; i < memoryProperties.memoryTypeCount; ++i) {
        // If our resource supports a memory type, and
        //  a memory type contains all required properties, then
        //  return the index of this memory type
        if (
            // Check resource (buffer) supports this memory type
            (memoryTypeBits & (1 << i)) &&
            // Check all required properties are supported by this memory type.
            //  `x&y==y` <=> `x contains y` <=> `All 1 bits in y are 1 bits in x`
            //  <=> All features of `properties` are in `memoryTypes[i].propertyFlags`
            ((memoryProperties.memoryTypes[i].propertyFlags & properties)==properties)
        ) {
            return i;
        }
    }
    return -1;
}
```

We then allocate and bind memory as such:

```cpp
// Allocates memory
VK_CHECK_RESULT(vkAllocateMemory(device, &allocateInfo, nullptr, bufferMemory));

// Binds allocated memory to buffer
VK_CHECK_RESULT(vkBindBufferMemory(device, *buffer, *bufferMemory, 0));
```