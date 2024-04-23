cmake_minimum_required(VERSION 3.10)
project(steamsdk-godot)

option(BUILD_SHARED_LIBS "Build shared libs" ON)

# Paths to godot-cpp and Steam SDK
set(GODOT_CPP_PATH "${CMAKE_CURRENT_SOURCE_DIR}/lib/godot-cpp")
set(STEAM_SDK_PATH "lib/steam-sdk" CACHE STRING "Steam SDK path")

# Include directories for headers
include_directories("${GODOT_CPP_PATH}/include")
include_directories("${GODOT_CPP_PATH}/include/core")
include_directories("${GODOT_CPP_PATH}/include/gen")
include_directories("${GODOT_CPP_PATH}/godot-headers")  # Adjusted for actual header location

# Linking directories
link_directories("${GODOT_CPP_PATH}/bin")  # Correct path to the built libraries

# Define the library to build
add_library(steamsdk-godot SHARED src/Godot.cpp)

set_target_properties(steamsdk-godot PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON
)

target_include_directories(steamsdk-godot PUBLIC ${STEAM_SDK_PATH}/public)

if(APPLE)
    set_target_properties(steamsdk-godot PROPERTIES
        OSX_ARCHITECTURES "arm64"
    )

    find_library(STEAM_API NAMES libsteam_api steam_api REQUIRED PATHS ${STEAM_SDK_PATH}/redistributable_bin/osx)
else()
    find_library(STEAM_API NAMES libsteam_api steam_api64 steam_api REQUIRED PATHS ${STEAM_SDK_PATH}/redistributable_bin/win64 ${STEAM_SDK_PATH}/redistributable_bin/linux64)
endif()

# Link libraries
target_link_libraries(steamsdk-godot PRIVATE
    ${STEAM_API}
    # Specify the actual library file name without the lib prefix and the .a extension
    godot-cpp.osx.release.64  # Adjust this line to match your godot-cpp library file name
)

install(TARGETS steamsdk-godot DESTINATION .)
