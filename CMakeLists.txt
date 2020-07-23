cmake_minimum_required(VERSION 3.12)
project(cppelix-celix-shim)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE CXX STANDARD REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_VERBOSE_MAKEFILE ON)

if(NOT CMAKE_BUILD_TYPE)
    SET(CMAKE_BUILD_TYPE Debug CACHE STRING
            "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
            FORCE)
endif(NOT CMAKE_BUILD_TYPE)

set(CMAKE_BINARY_DIR ${CMAKE_SOURCE_DIR}/bin)
set(TOP_DIR ${CMAKE_SOURCE_DIR})
set(EXTERNAL_DIR ${CMAKE_SOURCE_DIR}/external/)
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR})

option(BUILD_EXAMPLES "Build examples" ON)
option(USE_SANITIZERS "Enable sanitizers, catching potential errors but slowing down compilation and execution speed" ON)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DCATCH_CONFIG_FAST_COMPILE ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wshadow -Wnon-virtual-dtor -Wno-unused-variable -Wno-long-long -Wno-unused-parameter -Wduplicated-cond -Wduplicated-branches -Wlogical-op -Wnull-dereference -pedantic -Wformat -Wformat-security ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fconcepts -pthread ")
if(USE_SANITIZERS)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsanitize=address,undefined ")
endif()
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fstack-protector-strong -fstack-clash-protection -fcf-protection -fPIE")
set(CMAKE_CXX_FLAGS_DEBUG "-g3 -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -g3 -ggdb -DNDEBUG")
set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "-g3 -ggdb -Og")
set(CMAKE_CXX_FLAGS_MINSIZEREL "-Os -DNDEBUG")

find_package (Threads)

find_library(CPPELIX_LIBRARY cppelix HINTS ${EXTERNAL_DIR}/cppelix/bin)

if(NOT CPPELIX_LIBRARY)
    message(FATAL_ERROR "libcppelix not found")
endif()

file(GLOB_RECURSE SHIM_SOURCES ${TOP_DIR}/src/*.cpp)

add_library(cppelix_celix_shim ${SHIM_SOURCES})
target_link_libraries(cppelix_celix_shim ${CPPELIX_LIBRARY})

include_directories("${EXTERNAL_DIR}/celix/libs/framework/include")
include_directories("${EXTERNAL_DIR}/celix/libs/utils/include")
include_directories("${EXTERNAL_DIR}/cppelix/include")
include_directories("${EXTERNAL_DIR}/cppelix/external/concurrentqueue")
include_directories("${EXTERNAL_DIR}/cppelix/external/wyhash")
include_directories("${TOP_DIR}/include")

if(BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()