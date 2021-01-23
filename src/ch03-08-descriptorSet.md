## Creating a descriptor set

This section refers to:
- `Utility::createDescriptorSet`

Quite a bit of code, but all relatively simple.

We create 2 things:
1. Descriptor pool.
2. Descriptor set.

### Descriptor pool

A descriptor pool specifies a number of descriptors of each type, and when we create our descriptor set it pulls a number of descriptors of each type from our descriptor pool.

A descriptor set is initialized to contain the number of descriptors defined the given descriptor set layout, therefore our pool size must be more than or equal to the number of descriptors defined in our descriptor set layout.

`VK_DESCRIPTOR_TYPE_STORAGE_BUFFER` is the standard buffer type for most compute operations.

```cpp
// Descriptor type and number
VkDescriptorPoolSize descriptorPoolSize = {
    .type = VK_DESCRIPTOR_TYPE_STORAGE_BUFFER,
    .descriptorCount = 1 // Number of descriptors
};
// Creates descriptor pool
// A pool allocates a number of descriptors of each type
VkDescriptorPoolCreateInfo descriptorPoolCreateInfo = {
    .maxSets = 1, // max number of sets that can be allocated from this pool
    .poolSizeCount = 1, // length of `pPoolSizes`
    .pPoolSizes = &descriptorPoolSize // pointer to array of `VkDescriptorPoolSize`
};
// create descriptor pool.
VK_CHECK_RESULT(vkCreateDescriptorPool(
    device, &descriptorPoolCreateInfo, nullptr, descriptorPool
));
```

### Descriptor set

Allocates 1 descriptor set from pool.

```cpp
// Specifies options for creation of multiple of descriptor sets
VkDescriptorSetAllocateInfo descriptorSetAllocateInfo = {
    // pool from which sets will be allocated
    .descriptorPool = *descriptorPool, 
    // number of descriptor sets to implement (length of `pSetLayouts`)
    .descriptorSetCount = 1,
    // pointer to array of descriptor set layouts
    .pSetLayouts = descriptorSetLayout 
};
// allocate descriptor set.
VK_CHECK_RESULT(vkAllocateDescriptorSets(
    device, &descriptorSetAllocateInfo, &descriptorSet
));
```

Binds our 1 descriptor to our 1 buffer.

```cpp
// Defines buffer binding
VkDescriptorBufferInfo binding = {
    .buffer = buffer,
    .offset = 0,
    .range = VK_WHOLE_SIZE // set to whole size of buffer
};

// Binds descriptors from descriptor sets to buffers
VkWriteDescriptorSet writeDescriptorSet = {
    // write to this descriptor set.
    .dstSet = descriptorSet,
    // update 1 descriptor respective set (we only have 1).
    .descriptorCount = 1,
    // buffer type.
    .descriptorType = VK_DESCRIPTOR_TYPE_STORAGE_BUFFER,
    // respective buffer.
    .pBufferInfo = &binding
};

// perform the update of the descriptor set.
vkUpdateDescriptorSets(device, 1, &writeDescriptorSet, 0, nullptr);
```