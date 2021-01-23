## Reading a buffer

This section refers to:
- `Utility::map`

Much like how we fill a buffer except we do not unmap our buffer (in our use case we do not need to reuse it).

```cpp
void* data = nullptr;
vkMapMemory(device, bufferMemory, 0, VK_WHOLE_SIZE, 0, &data);
return data;
```