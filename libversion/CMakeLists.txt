include(GenerateExportHeader)

# sources
configure_file(config.h.in config.h @ONLY)

set(LIBVERSION_SOURCES
	private/compare.c
	private/parse.c
	compare.c
)

set(LIBVERSION_HEADERS
	version.h
)

set(LIBVERSION_PRIVATE_HEADERS
	private/compare.h
	private/component.h
	private/parse.h
	private/string.h
)

# shared library
add_library(libversion_libversion SHARED ${LIBVERSION_SOURCES} ${LIBVERSION_HEADERS} ${LIBVERSION_PRIVATE_HEADERS})
add_library(libversion::libversion ALIAS libversion_libversion)
target_include_directories(libversion_libversion PUBLIC
	"$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}>"
	"$<BUILD_INTERFACE:${PROJECT_BINARY_DIR}>"
	"$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)
target_compile_features(libversion_libversion PUBLIC c_std_99)
set_target_properties(libversion_libversion PROPERTIES
	VERSION "${PROJECT_VERSION}"
	SOVERSION "${PROJECT_VERSION_MAJOR}"
	OUTPUT_NAME version
	EXPORT_NAME libversion
	C_VISIBILITY_PRESET hidden
)
generate_export_header(libversion_libversion
	BASE_NAME libversion
	EXPORT_FILE_NAME export.h
)
if(WIN32)
	# avoid clash with both c:/windows/system32/version.dll
	# and static version.lib from the next target
	set_target_properties(libversion PROPERTIES OUTPUT_NAME libversion)
endif()

# static library
add_library(libversion_libversion_static STATIC ${LIBVERSION_SOURCES} ${LIBVERSION_HEADERS})
add_library(libversion::libversion_static ALIAS libversion_libversion_static)
add_dependencies(libversion_libversion_static libversion_libversion) # make sure export header is generated
target_include_directories(libversion_libversion_static PUBLIC
	"$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}>"
	"$<BUILD_INTERFACE:${PROJECT_BINARY_DIR}>"
	"$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)
target_compile_definitions(libversion_libversion_static PUBLIC LIBVERSION_STATIC_DEFINE)
target_compile_features(libversion_libversion_static PUBLIC c_std_99)
set_target_properties(libversion_libversion_static PROPERTIES
	POSITION_INDEPENDENT_CODE ON
	OUTPUT_NAME version
	EXPORT_NAME libversion_static
)

# installation
install(FILES
	${LIBVERSION_HEADERS}
	"${CMAKE_CURRENT_BINARY_DIR}/config.h"
	"${CMAKE_CURRENT_BINARY_DIR}/export.h"
	DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/libversion"
	COMPONENT libversion_Development
)
install(TARGETS
		libversion_libversion
		libversion_libversion_static
	EXPORT libversionTargets
	LIBRARY DESTINATION lib
	COMPONENT libversion_Runtime
	NAMELINK_COMPONENT libversion_Development
	ARCHIVE DESTINATION lib
	COMPONENT libversion_Development
)

# Allow package maintainers to freely override the path for the configs
set(
	libversion_INSTALL_CMAKEDIR "${CMAKE_INSTALL_DATADIR}/libversion"
	CACHE PATH "CMake package config location relative to the install prefix"
)
mark_as_advanced(libversion_INSTALL_CMAKEDIR)

set(pkgconfigdir "${CMAKE_INSTALL_LIBDIR}/pkgconfig")
if(CMAKE_SYSTEM_NAME MATCHES "FreeBSD" OR CMAKE_SYSTEM_NAME MATCHES "DragonFly")
	set(pkgconfigdir "libdata/pkgconfig")
endif()
set(libversion_INSTALL_PKGCONFIGDIR "${pkgconfigdir}" CACHE STRING "Directory where to install pkg-config files")
mark_as_advanced(libversion_INSTALL_PKGCONFIGDIR)

# Calculate relative paths to executables and libraries to support relocatable
# pkgconfig file
file(RELATIVE_PATH RELATIVE_INSTALL_LIBDIR_PREFIX
	"/${libversion_INSTALL_PKGCONFIGDIR}"
	"/"
)
# Strip trailing slash
string(REGEX REPLACE "/$" "" RELATIVE_INSTALL_LIBDIR_PREFIX "${RELATIVE_INSTALL_LIBDIR_PREFIX}")

# pkgconfig file
configure_file(libversion.pc.in libversion.pc @ONLY)

install(
	FILES "${CMAKE_CURRENT_BINARY_DIR}/libversion.pc"
	DESTINATION "${libversion_INSTALL_PKGCONFIGDIR}"
	COMPONENT libversion_Development
)

write_basic_package_version_file(
	libversionConfigVersion.cmake
	COMPATIBILITY SameMajorVersion
)

install(
	FILES "${CMAKE_CURRENT_BINARY_DIR}/libversionConfigVersion.cmake"
	DESTINATION "${libversion_INSTALL_CMAKEDIR}"
	COMPONENT libversion_Development
)

install(
	FILES "${PROJECT_SOURCE_DIR}/cmake/install-config.cmake"
	DESTINATION "${libversion_INSTALL_CMAKEDIR}"
	RENAME libversionConfig.cmake
	COMPONENT libversion_Development
)

install(
	EXPORT libversionTargets
	NAMESPACE libversion::
	DESTINATION "${libversion_INSTALL_CMAKEDIR}"
	COMPONENT libversion_Development
)

if(PROJECT_IS_TOP_LEVEL)
	include(CPack)
endif()
