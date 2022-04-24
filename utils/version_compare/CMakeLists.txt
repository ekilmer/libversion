add_executable(libversion_version_compare version_compare.c)
target_link_libraries(libversion_version_compare PRIVATE libversion::libversion)
target_compile_definitions(libversion_version_compare PRIVATE LIBVERSION_NO_DEPRECATED)
target_compile_features(libversion_version_compare PUBLIC c_std_99)
set_target_properties(
	libversion_version_compare PROPERTIES
	OUTPUT_NAME version_compare
)

install(
	TARGETS libversion_version_compare
	RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
	COMPONENT libversion_Utilities
)
