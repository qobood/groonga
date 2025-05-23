# Copyright (C) 2012-2018  Brazil
# Copyright (C) 2023-2024  Sutou Kouhei <kou@clear-code.com>
#
# This library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public
# License along with this library; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301  USA

read_file_list(${CMAKE_CURRENT_SOURCE_DIR}/create_dataset_sources.am
               GROONGA_SUGGEST_CREATE_DATASET_SOURCES)
add_executable(groonga-suggest-create-dataset
               ${GROONGA_SUGGEST_CREATE_DATASET_SOURCES})
set_source_files_properties(${GROONGA_SUGGEST_CREATE_DATASET_SOURCES}
                            PROPERTIES COMPILE_FLAGS "${GRN_C_COMPILE_FLAGS}")
target_link_libraries(groonga-suggest-create-dataset libgroonga)
install(TARGETS groonga-suggest-create-dataset
        DESTINATION "${CMAKE_INSTALL_BINDIR}")
if(MSVC)
  install(
    FILES $<TARGET_PDB_FILE:groonga-suggest-create-dataset>
    DESTINATION "${CMAKE_INSTALL_BINDIR}"
    OPTIONAL)
endif()

if(GRN_WITH_LIBEVENT
   AND GRN_WITH_ZEROMQ
   AND GRN_WITH_MESSAGE_PACK)
  set(GRN_WITH_SUGGEST_LEARNER TRUE)
else()
  set(GRN_WITH_SUGGEST_LEARNER FALSE)
endif()

if(GRN_WITH_SUGGEST_LEARNER)
  read_file_list(${CMAKE_CURRENT_SOURCE_DIR}/util_sources.am
                 GROONGA_SUGGEST_UTIL_SOURCES)
  add_library(groonga-suggest-util STATIC ${GROONGA_SUGGEST_UTIL_SOURCES})
  set_source_files_properties(${GROONGA_SUGGEST_UTIL_SOURCES}
                              PROPERTIES COMPILE_FLAGS "${GRN_C_COMPILE_FLAGS}")
  target_link_libraries(groonga-suggest-util libgroonga Groonga::libevent)

  read_file_list(${CMAKE_CURRENT_SOURCE_DIR}/learner_sources.am
                 GROONGA_SUGGEST_LEARNER_SOURCES)
  add_executable(groonga-suggest-learner ${GROONGA_SUGGEST_LEARNER_SOURCES})
  set_source_files_properties(${GROONGA_SUGGEST_LEARNER_SOURCES}
                              PROPERTIES COMPILE_FLAGS "${GRN_C_COMPILE_FLAGS}")
  target_link_libraries(
    groonga-suggest-learner
    groonga-suggest-util
    libgroonga
    Groonga::libevent
    Groonga::zeromq
    Groonga::msgpackc
    Threads::Threads)

  read_file_list(${CMAKE_CURRENT_SOURCE_DIR}/httpd_sources.am
                 GROONGA_SUGGEST_HTTPD_SOURCES)
  add_executable(groonga-suggest-httpd ${GROONGA_SUGGEST_HTTPD_SOURCES})
  set_source_files_properties(${GROONGA_SUGGEST_HTTPD_SOURCES}
                              PROPERTIES COMPILE_FLAGS "${GRN_C_COMPILE_FLAGS}")
  target_link_libraries(
    groonga-suggest-httpd
    groonga-suggest-util
    libgroonga
    Groonga::libevent
    Groonga::zeromq
    Groonga::msgpackc
    Threads::Threads)

  install(TARGETS groonga-suggest-learner groonga-suggest-httpd
          DESTINATION "${CMAKE_INSTALL_BINDIR}")
  if(MSVC)
    install(
      FILES $<TARGET_PDB_FILE:groonga-suggest-learner>
            $<TARGET_PDB_FILE:groonga-suggest-httpd>
      DESTINATION "${CMAKE_INSTALL_BINDIR}"
      OPTIONAL)
  endif()
endif()
