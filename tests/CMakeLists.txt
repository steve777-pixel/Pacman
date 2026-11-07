find_package(GTest CONFIG REQUIRED)
include(GoogleTest)

add_executable(pacman_tests
    test_maze.cpp
    test_level_loader.cpp
    test_collision.cpp
    test_ghost_ai.cpp
    test_pacman.cpp
    test_score_manager.cpp
)

target_link_libraries(pacman_tests PRIVATE
    pacman_core
    GTest::gtest
    GTest::gtest_main
)

# Registers each TEST() with CTest individually
gtest_discover_tests(pacman_tests)