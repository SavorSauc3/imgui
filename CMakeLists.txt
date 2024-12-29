cmake_minimum_required(VERSION 3.22)

# Define the project
project(imgui LANGUAGES CXX)

# Set the kind of project
add_library(imgui STATIC)

# Specify the source files
target_sources(imgui PRIVATE
    "${DepDir}/imgui/imconfig.h"
    "${DepDir}/imgui/imgui.h"
    "${DepDir}/imgui/imgui.cpp"
    "${DepDir}/imgui/imgui_draw.cpp"
    "${DepDir}/imgui/imgui_internal.h"
    "${DepDir}/imgui/imgui_tables.cpp"
    "${DepDir}/imgui/imgui_widgets.cpp"
    "${DepDir}/imgui/imstb_rectpack.h"
    "${DepDir}/imgui/imstb_textedit.h"
    "${DepDir}/imgui/imstb_truetype.h"
    "${DepDir}/imgui/imgui_demo.cpp"
    "${DepDir}/imgui/backends/imgui_impl_glfw.h"
    "${DepDir}/imgui/backends/imgui_impl_glfw.cpp"
    "${DepDir}/imgui/backends/imgui_impl_opengl3.h"
    "${DepDir}/imgui/backends/imgui_impl_opengl3.cpp"
    "${DepDir}/imgui/backends/imgui_impl_vulkan.h"
    "${DepDir}/imgui/backends/imgui_impl_vulkan.cpp"
)

# Include directories
target_include_directories(imgui PUBLIC
    ${IncludeDir_imgui}
    ${IncludeDir_glfw}
    ${IncludeDir_vulkanSDK}
)

# Link libraries
target_link_libraries(imgui PUBLIC
    glfw
    vulkan
)

# Set compiler options based on the system
if(WIN32)
    target_compile_features(imgui PRIVATE cxx_std_20)
    target_link_libraries(imgui PRIVATE glfw ${LibDir_vulkanSDK}/vulkan-1.lib)
    set_target_properties(imgui PROPERTIES
        MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
elseif(UNIX)
    target_compile_options(imgui PRIVATE -fPIC)
    target_compile_features(imgui PRIVATE cxx_std_20)
    target_link_libraries(imgui PRIVATE glfw vulkan)
endif()

# Set configurations
target_compile_definitions(imgui PRIVATE
    $<$<CONFIG:Debug>:DEBUG>
    $<$<CONFIG:Release>:RELEASE>
    $<$<CONFIG:Dist>:DIST>
)

# Set optimization and runtime options based on configurations
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -O0 -g")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3")
set(CMAKE_CXX_FLAGS_DIST "${CMAKE_CXX_FLAGS_DIST} -O3 -DNDEBUG")