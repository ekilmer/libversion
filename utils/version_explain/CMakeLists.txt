add_executable(libversion_version_explain version_explain.c)
# Must link to static component because this utility uses private functions
target_link_libraries(libversion_version_explain PRIVATE libversion::libversion_static)
target_compile_definitions(libversion_version_explain PRIVATE LIBVERSION_NO_DEPRECATE)
target_compile_features(libversion_version_explain PUBLIC c_std_99)
set_target_properties(
	libversion_version_explain PROPERTIES
	OUTPUT_NAME version_explain
)

install(
	TARGETS libversion_version_explain
	RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
	COMPONENT libversion_Utilities
)
