cmake_minimum_required(VERSION 3.12)

project(libversion-test LANGUAGES C)

include(../cmake/project-is-top-level.cmake)

if(PROJECT_IS_TOP_LEVEL)
	find_package(libversion REQUIRED)
	enable_testing()
endif()

add_executable(libversion_compare_test compare_test.c)
target_compile_features(libversion_compare_test PRIVATE c_std_99)
target_link_libraries(libversion_compare_test libversion::libversion)
set_target_properties(
	libversion_compare_test PROPERTIES
	OUTPUT_NAME compare_test
)
add_test(NAME libversion_compare_test COMMAND libversion_compare_test)

add_executable(libversion_compare_fuzzer compare_fuzzer.c)
target_compile_features(libversion_compare_fuzzer PRIVATE c_std_99)
target_link_libraries(libversion_compare_fuzzer libversion::libversion)
set_target_properties(
	libversion_compare_fuzzer PROPERTIES
	OUTPUT_NAME compare_fuzzer
)
add_test(NAME libversion_compare_fuzzer COMMAND libversion_compare_fuzzer)
