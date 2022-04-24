add_executable(libversion_version_sort version_sort.cc)
target_link_libraries(libversion_version_sort PRIVATE libversion::libversion)
target_compile_definitions(libversion_version_sort PRIVATE LIBVERSION_NO_DEPRECATED)
set_target_properties(
	libversion_version_sort PROPERTIES
	OUTPUT_NAME version_sort
)
target_compile_features(libversion_version_sort PRIVATE cxx_std_11)

install(
	TARGETS libversion_version_sort
	RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
	COMPONENT libversion_Utilities
)
