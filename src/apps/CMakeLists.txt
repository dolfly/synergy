# SPDX-FileCopyrightText: 2024 Deskflow Developers
# SPDX-FileCopyrightText: 2012 - 2024 Symless Ltd
# SPDX-FileCopyrightText: 2009 - 2012 Nick Bolton
# SPDX-License-Identifier: MIT

if(UNIX AND NOT APPLE)
  find_program(HELP2MAN help2man)
  if(NOT HELP2MAN)
    message(STATUS "Man page tool (help2man) not found, man pages will not be generated")
  endif()
endif()

function(generate_app_man TARGET NAME)
  if(HELP2MAN)
    add_custom_command(
      TARGET ${target} POST_BUILD
        COMMAND QT_QPA_PLATFORM=minimal PATH=$<TARGET_FILE_DIR:${target}>:${PATH} ${HELP2MAN}
          --name ${NAME}
          --include ${CMAKE_SOURCE_DIR}/src/apps/res/manpage.txt
          --no-info
          ${target}
          -o $<TARGET_FILE_DIR:${target}>/${target}.1
    )
    install(
      FILES $<TARGET_FILE_DIR:${target}>/${target}.1
      DESTINATION ${CMAKE_INSTALL_MANDIR}/man1
    )
  endif()
endfunction()

option(BUILD_UNIFIED "Build unified binary" OFF)
if(BUILD_UNIFIED)
  add_subdirectory(deskflow-core)
else()
  add_subdirectory(deskflow-client)
  add_subdirectory(deskflow-server)
endif(BUILD_UNIFIED)

## Only used on windows
add_subdirectory(deskflow-daemon)

option(BUILD_GUI "Build GUI" ON)
if(BUILD_GUI)
  add_subdirectory(deskflow-gui)
endif(BUILD_GUI)
