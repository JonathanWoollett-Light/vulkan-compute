## Creating an instance

This section refers to:
- `Utility::createInstance`

To begin we create an instance of the Vulkan.

For now we only need consider 3 parameters:

1. **Enabled layers:** What validation layers we will enable.
2. **Enabled extensions:** What feature extensions we will enable.
3. **API version:** What version of Vulkan we are targeting.

Validation layers are optional components that hook into Vulkan function calls to apply additional operations, typically (and in this case) for providing debug information.

We implement the typical validation layer `VK_LAYER_KHRONOS_validation` and the extension `VK_EXT_DEBUG_REPORT` to support it, to provide clearer error messages.

### Setup

If we are not in debug we do not include validation layers, else we do.

```cpp
#ifdef NDEBUG
const auto enableValidationLayers = std::nullopt;
#else
const auto enableValidationLayers = std::optional<char const*>{"VK_LAYER_KHRONOS_validation"};
#endif
```

In our procedure we first define the vectors within which we will store our enabled layers and extensions, and then check if our `enableValidationLayers` variable contains a value.

```cpp
std::vector<char const*> enabledLayers;
std::vector<char const*> enabledExtensions;

if (enableValidationLayers.has_value()) {
    // ...
}
```

Before we push our desired layers and extensions it is best to first check they are supported.

### Layers

Checking and pushing our validation layer:

```cpp
// Gets number of supported layers
uint32_t layerCount;
vkEnumerateInstanceLayerProperties(&layerCount, nullptr);
// Gets all supported layers
std::vector<VkLayerProperties> layerProperties(layerCount);
vkEnumerateInstanceLayerProperties(&layerCount, layerProperties.data());

// Check 'VK_LAYER_KHRONOS_validation' is among supported layers
auto layer_itr = std::find_if(layerProperties.begin(), layerProperties.end(), 
    [](VkLayerProperties& prop) {
        return (strcmp(enableValidationLayers.value(), prop.layerName) == 0);
    }
);
// If not, throw error
if (layer_itr == layerProperties.end()) {
    throw std::runtime_error("Validation layer not supported\n");
}
// Else, push to layers
enabledLayers.push_back(enableValidationLayers.value());
```

### Extensions

Checking and pushing our extension.

`std::find_if` not used here as it is more typical to require additional extensions and this approach better supports that.

```cpp
// Gets number of supported extensions
uint32_t extensionCount;
vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, nullptr);
// Gets all supported extensions
std::vector<VkExtensionProperties> extensionProperties(extensionCount);
vkEnumerateInstanceExtensionProperties(
    nullptr, &extensionCount, extensionProperties.data()
);

// Check `VK_EXT_DEBUG_REPORT` is among supported extensions
bool debug_report = false;
for(VkExtensionProperties& prop: extensionProperties) {
    if (strcmp(VK_EXT_DEBUG_REPORT_EXTENSION_NAME, prop.extensionName) == 0) { 
        debug_report=true;
        break;
    }
}
// If not, throw error
if (!debug_report) {
    throw std::runtime_error(
        "Extension VK_EXT_DEBUG_REPORT_EXTENSION_NAME not supported\n"
    );
}
// Else, push to extensions
enabledExtensions.push_back(VK_EXT_DEBUG_REPORT_EXTENSION_NAME);
```

### API version

To create an instance we need to create a `VkInstanceCreateInfo` object within which is a `VkApplicationInfo` object, it is within this object that we set our Vulkan version.

In the following code we set enabled layer, enabled extensions and the version to `VK_API_VERSION_1_1` (version 1.1 of Vulkan).

```cpp
VkApplicationInfo applicationInfo = {
    .apiVersion = VK_API_VERSION_1_1 // Vulkan version
};
VkInstanceCreateInfo createInfo = {
    .pApplicationInfo = &applicationInfo,
    .enabledLayerCount = static_cast<uint32_t>(enabledLayers.size()),
    .ppEnabledLayerNames = enabledLayers.data(),
    .enabledExtensionCount = static_cast<uint32_t>(enabledExtensions.size()),
    .ppEnabledExtensionNames = enabledExtensions.data()
};
```

### Creating an instance

We create our instance according to `createInfo`, store it in `instance` and check the result via our `VK_CHECK_RESULT` macro.

```cpp
VK_CHECK_RESULT(vkCreateInstance(
    &createInfo,
    nullptr,
    &instance)
);
```