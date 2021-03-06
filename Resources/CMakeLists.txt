
# this functionality now provided by install code in ../CMakeLists.txt
#
# if(RESDIR_DEFINED)
# 	add_custom_target(copy_resources ALL COMMENT "Copying resources")
# 	install(FILES
# 		${RESDIR}/pak000-Base.pak
# 		${RESDIR}/pak001-Sounds.pak
# 		${RESDIR}/pak002-Models.pak
# 		${RESDIR}/pak010-BaseSkin.pak
# 		${RESDIR}/pak999-References.pak
# 			DESTINATION share/games/openspades/Resources)
#
# 	file(GLOB_RECURSE RESOURCES Shaders/* Scripts/* Icons/* Killfeed/*)
# 	foreach(FILE ${RESOURCES})
# 		string(REPLACE ${CMAKE_CURRENT_SOURCE_DIR} ${RESDIR} TARGETFILE ${FILE})
# 		get_filename_component(TARGETDIR ${TARGETFILE} PATH)
# 		add_custom_command(TARGET copy_resources COMMAND ${CMAKE_COMMAND} -E make_directory "${TARGETDIR}")
# 		add_custom_command(TARGET copy_resources COMMAND ${CMAKE_COMMAND} -E copy_if_different "${FILE}" "${TARGETFILE}")
# 	endforeach()
# endif()


if(OPENSPADES_RESOURCES)
	add_custom_target(OpenSpades_Resources ALL COMMENT "Downloading external resources")

	add_custom_command(
		TARGET OpenSpades_Resources
		COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/downloadpak.sh ${CMAKE_CURRENT_SOURCE_DIR}
		WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

endif()

if(UNIX AND NOT APPLE)
	add_custom_target(OpenSpades_Unix ALL COMMENT "Process unix stuff")

	file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Icons)
	add_custom_command(
		TARGET OpenSpades_Unix
		COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/Icons/converticons.sh ${CMAKE_CURRENT_SOURCE_DIR}/Icons
		WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Icons)

	file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Unix)
	file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Unix/Debian)
	file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Unix/Man)

	configure_file("${CMAKE_CURRENT_SOURCE_DIR}/Unix/Debian/copyright.in" "${CMAKE_CURRENT_BINARY_DIR}/Unix/Debian/copyright")
	configure_file("${CMAKE_CURRENT_SOURCE_DIR}/Unix/Man/openspades.6.in" "${CMAKE_CURRENT_BINARY_DIR}/Unix/Man/openspades.6")

	add_custom_command(
		TARGET OpenSpades_Unix
		COMMAND sh -c \"gzip -9 -c ${CMAKE_CURRENT_SOURCE_DIR}/Unix/Debian/changelog > changelog.gz\"
		WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Unix/Debian
	)
	add_custom_command(
		TARGET OpenSpades_Unix
		COMMAND sh -c \"gzip -9 -c ${CMAKE_CURRENT_BINARY_DIR}/Unix/Man/openspades.6 > openspades.6.gz\"
		WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Unix/Man
	)
endif(UNIX AND NOT APPLE)
