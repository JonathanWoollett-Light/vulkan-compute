## Setup

At the top of the following sections I will write:

> This section refers to:

Followed by the methods in the completed project this section concerns.

### A class and a namespace

We will use a class `ComputeApp` to contain our Vulkan objects and a namespace `Utility` for our procedures to construct these objects.

```cpp
class ComputeApp {
    public:
        ComputeApp();
        ~ComputeApp();
}
namespace Utility {}
```

### Result checking

Many Vulkan procedures return a [`VkResult`](https://www.khronos.org/registry/vulkan/specs/1.2-extensions/man/html/VkResult.html) object specifying whether the operation was successful, for convenience here I will use a simple macro to check this.

```cpp
#define VK_CHECK_RESULT(f)                                                             \
{                                                                                      \
    VkResult res = (f);                                                                \
    if (res != VK_SUCCESS)                                                             \
    {                                                                                  \
        printf("Fatal : VkResult is %d in %s at line %d\n", res,  __FILE__, __LINE__); \
        assert(res == VK_SUCCESS);                                                     \
    }                                                                                  \
}
```