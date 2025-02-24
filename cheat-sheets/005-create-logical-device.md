# Create logical device

## Description
We use logical device to do work on GPU. Logical device is a selection of physical device and queues.

## Prerequisites
- [Select a physical device](./004-physical-devices-queue-families.md)

## Recipe
1. **Create logical device**:
```cpp
    void createLogicalDevice() {

        // Enumerate required features
        VkPhysicalDeviceFeatures deviceFeatures{};

        // Setup device queue creation info
        QueueFamilyIndices indices = findQueueFamiliesForFamily(m_physicalDevice, VK_QUEUE_GRAPHICS_BIT);
        VkDeviceQueueCreateInfo queueCreateInfo{};
        queueCreateInfo.sType = VK_STRUCTURE_TYPE_DEVICE_QUEUE_CREATE_INFO;
        queueCreateInfo.queueFamilyIndex = indices.graphicsFamily.value();
        queueCreateInfo.queueCount = 1;
        float queuePriority = 1.0f;
        queueCreateInfo.pQueuePriorities = &queuePriority;

        // Setup device creation info
        VkDeviceCreateInfo createInfo{};
        createInfo.sType = VK_STRUCTURE_TYPE_DEVICE_CREATE_INFO;
        createInfo.pQueueCreateInfos = &queueCreateInfo;
        createInfo.queueCreateInfoCount = 1;
        createInfo.pEnabledFeatures = &deviceFeatures;
        createInfo.enabledExtensionCount = 0;
        if (enableValidationLayers) {
            createInfo.enabledLayerCount = static_cast<uint32_t>(validationLayers.size());
            createInfo.ppEnabledLayerNames = validationLayers.data();
        } else {
            createInfo.enabledLayerCount = 0;
        }

        // Create logical device
        if (vkCreateDevice(m_physicalDevice, &createInfo, nullptr, &m_device) != VK_SUCCESS) {
            throw std::runtime_error("failed to create logical device!");
        }

        // Get graphics queue handler
        vkGetDeviceQueue(m_device, indices.graphicsFamily.value(), 0, &m_graphicsQueue);
    }
```