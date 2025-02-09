# Enumerate supported / required extensions

## Description
Extensions are features hardware supports. Different systems support different extensions.
We need to query system to identify which extensions are supported and check if program has all required extensions available.

## Prerequisites
- None

## Recipe
1. **List required extensions using GLFW**:
```cpp
    std::vector<const char*> getRequiredExtensions() {
        uint32_t extensionCount = 0;
        const char** extensions;
        extensions = glfwGetRequiredInstanceExtensions(&extensionCount);

        std::vector<const char*> extensions(extensions, extensions + extensionCount);

        if (enableValidationLayers) {
            extensions.push_back(VK_EXT_DEBUG_UTILS_EXTENSION_NAME);
        }

        return extensions;
    }
```

2. **Enumerate system supported extensions**:
```cpp
    bool areAllSupportedExtensionsAreSuported(const char** extensions, uint32_t extensionCount) {

        // Get number of extensions on system
        uint32_t extensionCount = 0;
        vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, nullptr);

        // Get extensions on system
        std::vector<VkExtensionProperties> extensions(extensionCount);
        vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, extensions.data());

        // Loop all over them to check if any is missing
        bool hasAllExtensions = true;
        for(uint32_t i = 0; i < extensionCount; i++) {

            bool isExtensionOnRequiredList = false;
            for (const auto& extension : extensions) {
                if(!strcmp(extensions[i], extension.extensionName)) {
                    isExtensionOnRequiredList = true;
                    break;
                }
            }

            if(isExtensionOnRequiredList) {
                std::cout << "\t[OK]" << extensions[i] << '\n';
            }
            else {
                hasAllExtensions = false;
                std::cout << "\t[NOK]" << extensions[i] << '\n';
            }
        }

        return hasAllExtensions;
    }
```