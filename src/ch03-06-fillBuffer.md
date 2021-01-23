## Filling a buffer

This section refers to:
- `Utility::fillBuffer`

To set the values of buffer.

```cpp
void* data = nullptr;
// Maps buffer memory into RAM
vkMapMemory(device, bufferMemory, 0, VK_WHOLE_SIZE, 0, &data);
// Fills buffer memory
memcpy(data, bufferData, static_cast<size_t>(bufferSize * sizeof(float)));
// Un-maps buffer memory from RAM to device memory
vkUnmapMemory(device, bufferMemory);
```