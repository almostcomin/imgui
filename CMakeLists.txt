cmake_minimum_required(VERSION 3.10)
project(imgui LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(IMGUI_SRC
    imgui.cpp
    imgui_draw.cpp
    imgui_widgets.cpp
    imgui_tables.cpp
)

list(APPEND IMGUI_SRC
    backends/imgui_impl_dx12.cpp
    backends/imgui_impl_win32.cpp
    backends/imgui_impl_sdl3.cpp
)

add_library(imgui STATIC ${IMGUI_SRC})

set(SDL3_INCLUDE_DIR "${CMAKE_CURRENT_LIST_DIR}/../../SDL/$<CONFIG>/include")

target_include_directories(imgui PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    # ${CMAKE_CURRENT_SOURCE_DIR}/backends
    ${SDL3_INCLUDE_DIR}    
)

# --- Installation

set(IMGUI_HEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui.h
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui_internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/imconfig.h
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_win32.h
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_sdl3.h
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_dx12.h
)

install(TARGETS imgui
    EXPORT imguiTargets
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

install(FILES ${IMGUI_HEADERS} DESTINATION include/imgui)