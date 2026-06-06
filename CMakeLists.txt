cmake_minimum_required(VERSION 3.10)
project(imgui LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Options
option(IMGUI_ENABLE_SDL3  "Enable SDL3 platform backend" ON)
option(IMGUI_ENABLE_WIN32 "Enable Win32 platform backend" OFF)
option(IMGUI_ENABLE_DX12  "Enable DirectX 12 backend" OFF)

# --- Validation: SDL3_INCLUDE_DIR is required if SDL3 is enabled ---
if(${IMGUI_ENABLE_SDL3} AND NOT DEFINED SDL3_INCLUDE_DIR)
    message(FATAL_ERROR 
        "SDL3_INCLUDE_DIR must be specified when IMGUI_ENABLE_SDL3=ON.\n"
        "Example: -DSDL3_INCLUDE_DIR=/path/to/SDL3/include"
    )
endif()

# Core sources
set(IMGUI_SRC
    imgui.cpp
    imgui_draw.cpp
    imgui_widgets.cpp
    imgui_tables.cpp
)

# Platform backends
if(${IMGUI_ENABLE_SDL3})
    list(APPEND IMGUI_SRC backends/imgui_impl_sdl3.cpp)
endif()

if(${IMGUI_ENABLE_WIN32})
    list(APPEND IMGUI_SRC backends/imgui_impl_win32.cpp)
endif()

# Rendering backends (disabled for your engine)
if(${IMGUI_ENABLE_DX12})
    list(APPEND IMGUI_SRC backends/imgui_impl_dx12.cpp)
endif()

# Static library
add_library(imgui STATIC ${IMGUI_SRC})

# Include directories
target_include_directories(imgui PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

if(${IMGUI_ENABLE_SDL3})
    target_include_directories(imgui PUBLIC ${SDL3_INCLUDE_DIR})
endif()

# Installation headers (only those used)
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