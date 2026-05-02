cmake_minimum_required(VERSION 3.13)
project(wasm_project C)

set(CMAKE_C_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Build raylib from the submodule
set(GRAPHICS GRAPHICS_API_OPENGL_ES3 CACHE STRING "" FORCE)
add_subdirectory(third_party/raylib)

add_executable(app src/main.c)
target_link_libraries(app PRIVATE raylib)

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