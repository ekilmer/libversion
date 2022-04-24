cmake_minimum_required(VERSION 3.12)

project(libversion
	VERSION 3.0.1
	DESCRIPTION "Advanced version string comparison library"
	HOMEPAGE_URL "https://github.com/repology/libversion"
	LANGUAGES C
)

include(cmake/project-is-top-level.cmake)

include(CMakePackageConfigHelpers)
include(GNUInstallDirs)

# subdirs

# Main library
add_subdirectory(libversion)

# Utility tools
if(UNIX)
	add_subdirectory(utils)
endif()

# Tests
include(CTest)
if(BUILD_TESTING)
	add_subdirectory(tests)
endif()
