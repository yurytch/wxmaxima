# -*- mode: CMake; cmake-tab-width: 4; -*-

add_custom_target(update-locale-manual)
set_target_properties(update-locale-manual
    PROPERTIES OUTPUT_NAME update-locale)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/po4a.cfg.in ${CMAKE_CURRENT_BINARY_DIR}/po4a.cfg)

if(NOT PO4A-UPDATEPO)
    find_program(PO4A-UPDATEPO po4a-updatepo)
endif()

file(GLOB POT_SOURCE_FILES ${CMAKE_CURRENT_SOURCE_DIR}/*.po)

if(PO4A-UPDATEPO)
    foreach(POFILE ${POT_SOURCE_FILES})
	string(REGEX REPLACE ".*/(.*).po$" "\\1" LANG ${POFILE})
	add_custom_command(
	    OUTPUT ${POFILE}
	    DEPENDS ${CMAKE_SOURCE_DIR}/info/wxmaxima.md
	    COMMAND ${PO4A-UPDATEPO} -f text -m ${CMAKE_SOURCE_DIR}/info/wxmaxima.md -p ${POFILE}  -M UTF-8
	    COMMENT "Updating ${POFILE} in order to sync the file with the changes in ${CMAKE_SOURCE_DIR}/info/wxmaxima.md"
	    )
	add_custom_target(
	    update_manual_po_${LANG} ALL
	    DEPENDS ${POFILE}
	    )
    endforeach()
else()
    message(WARNING "po4a-updatepo not found. .po files cannot be automatically updated with changes to the untranslated manual.")
endif()
