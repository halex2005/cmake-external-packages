#=============================================================================
# Copyright 2016 halex2005 <akharlov@gmail.com>
#
# This file is part of [cmake-external-packages project](https://github.com/halex2005/cmake-external-packages).
# Distributed under MIT License (the "License");
# see accompanying file LICENSE for details.
#
# This software is distributed WITHOUT ANY WARRANTY; without even the
# implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
# See the License for more information.
#=============================================================================
# (To distribute this file outside of cmake-external-packages, substitute the full
#  License text for the above reference.)
#=============================================================================
# Usage:
#     just include this file by `add_subdirectory(/path/to/this/file)` build_command
# Result:
#     will create tinyxml cmake target
#     use `target_link_libraries(your_exe tinyxml)` to add reference to tinyxml target
# Customization:
#     TINYXML_USE_STL ON/OFF - option for using STL's std::string or tinyxml iternal string inmplementation.
#     TINYXML_VERSION_TAG - tinyxml tag version from [sourceforge files](https://sourceforge.net/projects/tinyxml/files/tinyxml)
#         Though tested with 2.6.2 version only, should support any version from sourceforge.
#     EXTERNAL_PACKAGES_INCLUDE_DIR - directory where tinyxml interface includes will be copied.
#         You should add this directory to include_directories()
#=============================================================================
cmake_minimum_required(VERSION 2.8)

option(TINYXML_USE_STL "Build the TinyXml with STL strings" OFF)
set(TINYXML_VERSION_TAG "2.6.2" CACHE STRING "TinyXml version tag from github")
string(REPLACE "." "_" TINYXML_VERSION_TAG_UNDERSCORE ${TINYXML_VERSION_TAG})

set (EXTERNAL_PACKAGES_INCLUDE_DIR ${CMAKE_CURRENT_LIST_DIR}/../include
    CACHE STRING "Directory for third-party include files, where include folders will be copied")

set (TINYXML_ARCHIVE_PATH "${CMAKE_CURRENT_BINARY_DIR}/tinyxml-${TINYXML_VERSION_TAG_UNDERSCORE}.zip")
set (TINYXML_EXTRACT_ARCHIVE_WORKING_DIR ${CMAKE_CURRENT_LIST_DIR})
set (TINYXML_SOURCE_PATH ${TINYXML_EXTRACT_ARCHIVE_WORKING_DIR}/tinyxml-${TINYXML_VERSION_TAG}/)
set (TINYXML_INCLUDE_PATH ${EXTERNAL_PACKAGES_INCLUDE_DIR}/tinyxml/)

if (NOT EXISTS ${TINYXML_SOURCE_PATH})
    if (NOT EXISTS ${TINYXML_ARCHIVE_PATH})
        file(DOWNLOAD
            http://downloads.sourceforge.net/project/tinyxml/tinyxml/${TINYXML_VERSION_TAG}/tinyxml_${TINYXML_VERSION_TAG_UNDERSCORE}.zip
            ${TINYXML_ARCHIVE_PATH}
            SHOW_PROGRESS)
    endif()
    execute_process(
        COMMAND ${CMAKE_COMMAND} -E tar xvf ${TINYXML_ARCHIVE_PATH}
        WORKING_DIRECTORY ${TINYXML_EXTRACT_ARCHIVE_WORKING_DIR})
    file(RENAME ${TINYXML_EXTRACT_ARCHIVE_WORKING_DIR}/tinyxml ${TINYXML_SOURCE_PATH})
    if (NOT EXISTS ${TINYXML_SOURCE_PATH})
        message(FATAL_ERROR "Please, check where tinyxml was extracted")
    endif()
endif()

if (NOT EXISTS ${TINYXML_INCLUDE_PATH})
    file(COPY "${TINYXML_SOURCE_PATH}" DESTINATION "${TINYXML_INCLUDE_PATH}" FILES_MATCHING PATTERN "*.h")
    if (NOT EXISTS ${TINYXML_INCLUDE_PATH})
        message(FATAL_ERROR "Please, check where tinyxml include was copied")
    endif()
endif()

set (TINYXML_HEADERS
    ${TINYXML_SOURCE_PATH}/tinystr.h
    ${TINYXML_SOURCE_PATH}/tinyxml.h
)

set (TINYXML_SOURCES
    ${TINYXML_SOURCE_PATH}/tinystr.cpp
    ${TINYXML_SOURCE_PATH}/tinyxml.cpp
    ${TINYXML_SOURCE_PATH}/tinyxmlerror.cpp
    ${TINYXML_SOURCE_PATH}/tinyxmlparser.cpp
)

add_library(tinyxml STATIC ${TINYXML_SOURCES} ${TINYXML_HEADERS})
if (TINYXML_USE_STL)
    target_compile_definitions(tinyxml PUBLIC TIXML_USE_STL)
endif()

add_executable(TinyXmlTest ${TINYXML_SOURCE_PATH}/xmltest.cpp)
target_link_libraries(TinyXmlTest tinyxml)
