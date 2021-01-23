## Getting a physical device

This section refers to:
- `Utility::getPhysicalDevice`

Now we need to select the physical device we want to work with.

In most cases this will by our GPU and our only device.

In our code we:
1. Get the number of physical devices within the system.
2. Check it is not 0.
3. Get all our physical devices.
4. Set the device we will use as our 1st device.

```cpp
// Gets number of physical devices
uint32_t deviceCount;
vkEnumeratePhysicalDevices(instance, &deviceCount, nullptr);

// Asserts a system has a device
assert(deviceCount != 0);

// Gets physical devices
std::vector<VkPhysicalDevice> devices(deviceCount);
vkEnumeratePhysicalDevices(instance, &deviceCount, devices.data());

// Picks 1st
physicalDevice = devices[0];
```

The `assert(deviceCount != 0)` while unnecessary, is my preference to check.