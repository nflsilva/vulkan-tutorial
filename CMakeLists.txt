cmake_minimum_required(VERSION 3.26.4)

project(vulkan-tutorial)

find_package(Vulkan REQUIRED)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Source files
file(GLOB_RECURSE SOURCES "source/*.cpp")


include_directories(
    "include"
    ${Vulkan_INCLUDE_DIRS}
)

# Add executable
add_executable(${PROJECT_NAME} ${SOURCES})

add_subdirectory(vendor/glfw)
add_subdirectory(vendor/glm)

target_link_libraries(${PROJECT_NAME} PRIVATE ${Vulkan_LIBRARIES} glfw glm)