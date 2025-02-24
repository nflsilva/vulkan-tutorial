# Physical devices and queue families

## Description
We list physical devices and their queues so we can check which device one fits the application needs.
We then select that device and queues for our application.

## Prerequisites
- [Create vulkan instance](./001-create-vulkan-instance.md)

## Recipe
1. **QueueFamilyIndices struct**: Create a new structure to hold the family of each queue:
```cpp
    struct QueueFamilyIndices {
        std::optional<uint32_t> graphicsFamily;
        bool isComplete() { return graphicsFamily.has_value(); }
    };
```

2. **Check if device has a queue of a specific family**: We return the index for that queue to be used on [logical device creation](./005-create-logical-device.md):
```cpp
    QueueFamilyIndices findQueueFamiliesForFamily(VkPhysicalDevice device, VkQueueFlagBits familyBit) {
        QueueFamilyIndices indices;

        uint32_t queueFamilyCount = 0;
        vkGetPhysicalDeviceQueueFamilyProperties(device, &queueFamilyCount, nullptr);

        std::vector<VkQueueFamilyProperties> queueFamilies(queueFamilyCount);
        vkGetPhysicalDeviceQueueFamilyProperties(device, &queueFamilyCount, queueFamilies.data());

        int i = 0;
        for (const auto& queueFamily : queueFamilies) {
            if (queueFamily.queueFlags & familyBit) {
                indices.graphicsFamily = i;
            }
            i++;
        }

        return indices;
    }
```

3. **Check if device has a queue of a specific family**:
```cpp
    bool isDeviceSuitable(VkPhysicalDevice device) {
        VkPhysicalDeviceProperties deviceProperties;
        vkGetPhysicalDeviceProperties(device, &deviceProperties);

        VkPhysicalDeviceFeatures deviceFeatures;
        vkGetPhysicalDeviceFeatures(device, &deviceFeatures);

        // We consider our application only usable by dedicated graphics cards 
        // that support geometry shaders
        return deviceProperties.deviceType == VK_PHYSICAL_DEVICE_TYPE_DISCRETE_GPU 
            && deviceFeatures.geometryShader
            && findQueueFamiliesForFamily(device, VK_QUEUE_GRAPHICS_BIT).isComplete();
    }
```

4. **Select the desired physical device**:
```cpp
    void pickPhysicalDevice() {

        uint32_t deviceCount = 0;
        vkEnumeratePhysicalDevices(m_instance, &deviceCount, nullptr);
        if (deviceCount == 0) {
            throw std::runtime_error("failed to find GPUs with Vulkan support!");
        }

        std::vector<VkPhysicalDevice> devices(deviceCount);
        vkEnumeratePhysicalDevices(m_instance, &deviceCount, devices.data());

        for (const VkPhysicalDevice& device : devices) {
            if (isDeviceSuitable(device)) {
                m_physicalDevice = device;
                break;
            }
        }

        if (m_physicalDevice == VK_NULL_HANDLE) {
            throw std::runtime_error("failed to find a suitable GPU!");
        }
    }
```