cmake_minimum_required(VERSION 3.12)

project(libversion-utils LANGUAGES C CXX)

include(../cmake/project-is-top-level.cmake)

if(PROJECT_IS_TOP_LEVEL)
	include(GNUInstallDirs)
	find_package(libversion REQUIRED)
endif()

add_subdirectory(version_compare)
add_subdirectory(version_sort)
add_subdirectory(version_explain)
