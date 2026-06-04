cmake_minimum_required(VERSION 3.10)
project(imgui LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(IMGUI_ENABLE_SDL3  "Enable SDL3 platform backend" ON)
option(IMGUI_ENABLE_WIN32 "Enable Win32 platform backend" OFF)
option(IMGUI_ENABLE_DX12  "Enable DirectX 12 backend" OFF)

set(IMGUI_SRC
    imgui.cpp
    imgui_draw.cpp
    imgui_widgets.cpp
    imgui_tables.cpp
)

if(${IMGUI_ENABLE_SDL3})
    list(APPEND IMGUI_SRC backends/imgui_impl_sdl3.cpp)
endif()

if(${IMGUI_ENABLE_WIN32})
    list(APPEND IMGUI_SRC backends/imgui_impl_win32.cpp)
endif()

if(${IMGUI_ENABLE_DX12})
    list(APPEND IMGUI_SRC backends/imgui_impl_dx12.cpp)
endif()

add_library(imgui STATIC ${IMGUI_SRC})

target_include_directories(imgui PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

# SDL3 include path (adjust to your installation structure)
if(${IMGUI_ENABLE_SDL3})
    # Assuming SDL3 is built and installed in ../../SDL/${CMAKE_BUILD_TYPE}/include
    # CMAKE_BUILD_TYPE is set by the build system (Debug, Release, etc.)
    set(SDL3_INCLUDE_DIR "${CMAKE_CURRENT_LIST_DIR}/../../SDL/${CMAKE_BUILD_TYPE}/include")
    target_include_directories(imgui PUBLIC ${SDL3_INCLUDE_DIR})
endif()

# Installation headers (only those that are used)
set(IMGUI_HEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui.h
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui_internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/imconfig.h
)

if(${IMGUI_ENABLE_SDL3})
    list(APPEND IMGUI_HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_sdl3.h)
endif()

install(TARGETS imgui
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

install(FILES ${IMGUI_HEADERS} DESTINATION include/imgui)