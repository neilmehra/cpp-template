cmake_minimum_required(VERSION 3.14)
project(cpp-template LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(GCC_WARNINGS
  -Wall
  -Wextra
  -Wpedantic
  -Wconversion
  -Wsign-conversion
  -Wshadow
  -Wnull-dereference
  -Wduplicated-cond
  -Wduplicated-branches
  -Wlogical-op
  -Wrestrict
  -Werror
)

add_compile_options(${GCC_WARNINGS})

# for clangd
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

enable_testing()

include_directories(include)

# header only
# add_library(cpp-template INTERFACE)
#target_include_directories(cpp-template
#  INTERFACE
#    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
#    $<INSTALL_INTERFACE:include>
#)

file(GLOB_RECURSE SOURCES "src/*.cpp")

add_library(cpp-template STATIC ${SOURCES})
target_include_directories(cpp-template PUBLIC include)

include(FetchContent)
FetchContent_Declare(
  googletest
  URL https://github.com/google/googletest/archive/refs/tags/v1.15.2.zip
)
FetchContent_MakeAvailable(googletest)

file(GLOB_RECURSE TEST_SOURCES "tests/*.cpp")
foreach(TEST_SOURCE ${TEST_SOURCES})
    get_filename_component(TEST_NAME ${TEST_SOURCE} NAME_WE)
    add_executable(${TEST_NAME} ${TEST_SOURCE})
    target_link_libraries(${TEST_NAME} cpp-template gtest gtest_main)
    add_test(NAME ${TEST_NAME} COMMAND ${TEST_NAME})
endforeach()


file(GLOB EXAMPLES_SOURCES "examples/*.cpp")
foreach(example_src ${EXAMPLES_SOURCES})
  get_filename_component(example_name ${example_src} NAME_WE)
  add_executable(${example_name} ${example_src})
  target_link_libraries(${example_name} PRIVATE cpp-template)
endforeach()
