# Copyright(C) 2013 Brazil
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

file(GLOB
  PLUGIN_CMAKE_LISTS_LIST
  "${CMAKE_CURRENT_SOURCE_DIR}/*/CMakeLists.txt")
if(PLUGIN_CMAKE_LISTS_LIST)
  foreach(PLUGIN_CMAKE_LISTS "${PLUGIN_CMAKE_LISTS_LIST}")
    get_filename_component(PLUGIN_DIR "${PLUGIN_CMAKE_LISTS}" DIRECTORY)
    get_filename_component(PLUGIN_DIR_RELATIVE "${PLUGIN_DIR}" NAME)
    add_subdirectory("${PLUGIN_DIR_RELATIVE}")
  endforeach(PLUGIN_CMAKE_LISTS)
endif()
