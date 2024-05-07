cmake_minimum_required(VERSION 3.14)

project(libstream)

# Set the source files
set(SOURCE_FILES
    emscripten.cpp
    )

# Define the target
add_executable(libstream ${SOURCE_FILES})

# Include default target options (optional, depends on your setup)
include(DefaultTargetOptions)

# Link with necessary libraries
target_link_libraries(libstream PRIVATE whisper)

# Set the WASM specific flags
set_target_properties(libstream PROPERTIES LINK_FLAGS "\
    -s STANDALONE_WASM \
    -s DISABLE_EXCEPTION_CATCHING=1 \
    -s USE_PTHREADS=1 \
    -s PTHREAD_POOL_SIZE=8 \
    -s INITIAL_MEMORY=1024MB \
    -s TOTAL_MEMORY=1024MB \
    -s NO_EXIT_RUNTIME=1 \
    -s EXPORTED_FUNCTIONS='[_main]' \
    -s EXTRA_EXPORTED_RUNTIME_METHODS='[]' \
    ")

# Add custom command to rename output to .wasm only
add_custom_command(
    TARGET libstream POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E rename
    ${CMAKE_BINARY_DIR}/libstream.wasm
    ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/libstream.wasm
)
