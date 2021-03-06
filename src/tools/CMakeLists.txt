# Change the location of executables
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set (TOOLS ON CACHE BOOL "Tools")

if(${TOOLS})
  # grap-match
  add_subdirectory(grap-match)

  # grap
  add_subdirectory(grap)

  # tests
  add_subdirectory(tests)

  # todot
  add_subdirectory(todot)
  
  # disassembler
  # On Windows the disassembler is not installer with setup.py, it is only available through bindings
  find_program(PYTHON "python")
  if (PYTHON)
    set(SETUP_PY_IN "${CMAKE_CURRENT_SOURCE_DIR}/setup.py")
    set(SETUP_PY    "${CMAKE_CURRENT_BINARY_DIR}/setup.py")
    if(NOT "${MSVC}")
      set(DEPS        "${CMAKE_CURRENT_SOURCE_DIR}/grap_disassembler/__init__.py")
    endif()
    set(OUTPUT      "${CMAKE_CURRENT_BINARY_DIR}/build/setup_timestamp")
  
    if(NOT "${MSVC}")
      configure_file(${SETUP_PY_IN} ${SETUP_PY})
  
      add_custom_command(OUTPUT ${OUTPUT}
                         COMMAND ${PYTHON} ${SETUP_PY} build
                         COMMAND ${CMAKE_COMMAND} -E touch ${OUTPUT}
                         DEPENDS ${DEPS})
  
      add_custom_target(target ALL DEPENDS ${OUTPUT})
  
      install(CODE "execute_process(COMMAND ${PYTHON} ${SETUP_PY} install)")
    endif()
  endif() 

endif(${TOOLS})
