file(GLOB_RECURSE PROJECT_CELIX_EXAMPLE_SOURCES ${TOP_DIR}/examples/celix_example/*.cpp)
add_executable(cppelix_celix_example ${PROJECT_CELIX_EXAMPLE_SOURCES})
target_link_libraries(cppelix_celix_example ${CMAKE_THREAD_LIBS_INIT})
target_link_libraries(cppelix_celix_example -ldl -lrt)
target_link_libraries(cppelix_celix_example cppelix_celix_shim)