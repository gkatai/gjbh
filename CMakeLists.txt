cmake_minimum_required(VERSION 3.13)
project(wasm_project C)

set(CMAKE_C_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Build raylib from the submodule
set(GRAPHICS GRAPHICS_API_OPENGL_ES3 CACHE STRING "" FORCE)
add_subdirectory(third_party/raylib)
# WebAssembly doesn't support dynamic linking
if(EMSCRIPTEN)
    set(FLECS_SHARED OFF CACHE BOOL "" FORCE)
endif()
add_subdirectory(third_party/flecs)

add_executable(app src/main.c)
target_link_libraries(app PRIVATE raylib)

if(EMSCRIPTEN)
    # flecs_static because shared libs aren't supported on WebAssembly
    target_link_libraries(app PRIVATE flecs_static)
else()
    target_link_libraries(app PRIVATE flecs)
endif()

if(EMSCRIPTEN)
    # Generate an HTML page to host the WASM module
    set_target_properties(app PROPERTIES
        SUFFIX ".html"
    )

    # WebAssembly flags
    target_link_options(app PRIVATE
        "-sUSE_GLFW=3"
        "-sMIN_WEBGL_VERSION=2"
        "-sMAX_WEBGL_VERSION=2"
        "-sASYNCIFY"
        "-sSTACK_SIZE=1048576"
    )

    # Debug flags for DWARF + source maps (Chrome DevTools)
    if(CMAKE_BUILD_TYPE STREQUAL "Debug")
        target_link_options(app PRIVATE
            "-g3"
            "-gseparate-dwarf"
            "-gsource-map"
        )
    endif()
endif()