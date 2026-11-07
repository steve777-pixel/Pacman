cmake_minimum_required(VERSION 3.20)
project(Pacman VERSION 1.0.0 LANGUAGES CXX)

# ---- C++ standard ----
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)   # helps VS Code IntelliSense

# ---- Options ----
option(BUILD_TESTS "Build unit tests" ON)

# ---- Dependencies (installed by vcpkg) ----
find_package(SFML 2.6 COMPONENTS system window graphics audio REQUIRED)

# ---- Core library: all game code except main.cpp ----
file(GLOB_RECURSE PACMAN_SOURCES CONFIGURE_DEPENDS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp
)
list(FILTER PACMAN_SOURCES EXCLUDE REGEX ".*/src/main\\.cpp$")

add_library(pacman_core STATIC ${PACMAN_SOURCES})

target_include_directories(pacman_core PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_link_libraries(pacman_core PUBLIC
    sfml-system sfml-window sfml-graphics sfml-audio
)

if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(pacman_core PUBLIC -Wall -Wextra -Wpedantic)
elseif(MSVC)
    target_compile_options(pacman_core PUBLIC /W4)
endif()

# ---- Executable ----
add_executable(pacman src/main.cpp)
target_link_libraries(pacman PRIVATE pacman_core)

# ---- Copy assets next to the executable so relative paths work ----
add_custom_command(TARGET pacman POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${CMAKE_CURRENT_SOURCE_DIR}/assets
        $<TARGET_FILE_DIR:pacman>/assets
)

# ---- Tests ----
if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()