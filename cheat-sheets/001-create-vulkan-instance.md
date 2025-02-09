# Create Vulkan Instance

## Description
Vulkan instance is used to interact with Vulkan driver and objects.
It is the point of communication between Vulkan and program.
Therefore, it is used for a lot of operations, such as:
- Enumerate physical devices
- Enumerate available extensions

## Prerequisites
- [List of required extensions](./002-supported-required-extensions.md) (required)
- [Setup of validation layers](./003-setup-validation-layers.md)  (optional)
    - VK_EXT_DEBUG_UTILS_EXTENSION_NAME extension
    - Debug messenger

## Recipe

1. **Setup VkApplicationInfo**: Contains application information:
```cpp
    // Setup application info
    VkApplicationInfo appInfo{};
    appInfo.sType = VK_STRUCTURE_TYPE_APPLICATION_INFO;
    appInfo.pApplicationName = "Hello Triangle";
    appInfo.applicationVersion = VK_MAKE_VERSION(1, 0, 0);
    appInfo.pEngineName = "No Engine";
    appInfo.engineVersion = VK_MAKE_VERSION(1, 0, 0);
    appInfo.apiVersion = VK_API_VERSION_1_0;
```

2. **Setup VkInstanceCreateInfo**: Contains instance creation information:
```cpp
    // Setup instance creation info struct
    VkInstanceCreateInfo createInfo{};
    createInfo.sType = VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO;
    createInfo.pApplicationInfo = &appInfo;
    createInfo.enabledExtensionCount = static_cast<uint32_t>(requiredExtensions.size());
    createInfo.ppEnabledExtensionNames = requiredExtensions.data();

    // Setup validation layers
    VkDebugUtilsMessengerCreateInfoEXT debugCreateInfo{};

    #ifdef NDEBUG
        constexpr bool enableValidationLayers = false;
    #else
        constexpr bool enableValidationLayers = true;
    #endif

    if (enableValidationLayers) {
        createInfo.enabledLayerCount = static_cast<uint32_t>(validationLayers.size());
        createInfo.ppEnabledLayerNames = validationLayers.data();

        populateDebugMessengerCreateInfo(debugCreateInfo);
        createInfo.pNext = (VkDebugUtilsMessengerCreateInfoEXT*) &debugCreateInfo;
    } else {
        createInfo.enabledLayerCount = 0;
        createInfo.pNext = nullptr;
    }

    // Create instance
    VkInstance instance;
    if (vkCreateInstance(&createInfo, nullptr, &instance) != VK_SUCCESS) {
        throw std::runtime_error("failed to create instance!");
    }
```