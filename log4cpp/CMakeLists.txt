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
#     will create liblog4cpp cmake target
#     use `target_link_libraries(your_exe liblog4cpp)` to add reference to liblog4cpp target
# Customization:
#     LOG4CPP_BUILD_SHARED_LIBS ON/OFF - option for build the log4cpp as shared/static libraries
#     LOG4CPP_VERSION_TAG - log4cpp tag version from [sourceforge files](https://sourceforge.net/projects/log4cpp/files)
#         only 1.1.1 version is supported for now.
#     EXTERNAL_PACKAGES_INCLUDE_DIR - directory where log4cpp interface includes will be copied.
#         You should add this directory to include_directories()
#=============================================================================
cmake_minimum_required(VERSION 2.8)

if (WIN32)

    option(LOG4CPP_BUILD_SHARED_LIBS "Build the log4cpp as shared library" OFF)
    set(LOG4CPP_VERSION_TAG "1.1.1" CACHE STRING "Log4cpp version tag from sourceforge")

    set (EXTERNAL_PACKAGES_INCLUDE_DIR ${CMAKE_CURRENT_LIST_DIR}/../include
        CACHE STRING "Directory for third-party include files, where include folders will be copied")

    set (LOG4CPP_TARGETS_FILE ${CMAKE_CURRENT_LIST_DIR}/log4cpp-${LOG4CPP_VERSION_TAG}.targets)
    if (NOT EXISTS ${LOG4CPP_TARGETS_FILE})
        message(FATAL_ERROR "Log4cpp v${LOG4CPP_VERSION_TAG} is not supported (${LOG4CPP_TARGETS_FILE} file does not exists")
    endif()

    set (LOG4CPP_ARCHIVE_PATH "${CMAKE_CURRENT_BINARY_DIR}/log4cpp-${LOG4CPP_VERSION_TAG}.tar.gz")
    set (LOG4CPP_EXTRACT_ARCHIVE_WORKING_DIR ${CMAKE_CURRENT_LIST_DIR})
    set (LOG4CPP_SOURCE_PATH ${LOG4CPP_EXTRACT_ARCHIVE_WORKING_DIR}/log4cpp/)
    set (LOG4CPP_INCLUDE_PATH ${EXTERNAL_PACKAGES_INCLUDE_DIR}/log4cpp/)

    remove_definitions(-DUNICODE)
    remove_definitions(-D_UNICODE)

    if (NOT EXISTS ${LOG4CPP_SOURCE_PATH})
        if (NOT EXISTS ${LOG4CPP_ARCHIVE_PATH})
            file(DOWNLOAD
                http://downloads.sourceforge.net/project/log4cpp/log4cpp-1.1.x%20%28new%29/log4cpp-1.1/log4cpp-1.1.tar.gz
                ${LOG4CPP_ARCHIVE_PATH}
                SHOW_PROGRESS)
        endif()
        execute_process(
            COMMAND ${CMAKE_COMMAND} -E tar xvf ${LOG4CPP_ARCHIVE_PATH}
            WORKING_DIRECTORY ${LOG4CPP_EXTRACT_ARCHIVE_WORKING_DIR})
        if (NOT EXISTS ${LOG4CPP_SOURCE_PATH})
            message(FATAL_ERROR "Please, check where log4cpp was extracted")
        endif()
    endif()

    if (NOT EXISTS ${LOG4CPP_INCLUDE_PATH})
        file(COPY "${LOG4CPP_SOURCE_PATH}/include/log4cpp" DESTINATION "${EXTERNAL_PACKAGES_INCLUDE_DIR}")
        if (NOT EXISTS ${LOG4CPP_INCLUDE_PATH})
            message(FATAL_ERROR "Please, check where log4cpp include was copied")
        endif()
    endif()

    include_directories(
        "${LOG4CPP_SOURCE_PATH}/include/"
    )

    add_definitions("/wd4244 /wd4267 /wd4018 /wd4355 /wd4800 /wd4251 /wd4996 /wd4146 /wd4305")
    add_definitions("/W3")

    # Workaround for Visual Studio 2015 error C2338:
    #   <hash_map> is deprecated and will be REMOVED. Please use <unordered_map>.
    #   You can define _SILENCE_STDEXT_HASH_DEPRECATION_WARNINGS to acknowledge that you have received this warning.
    add_definitions("/D_SILENCE_STDEXT_HASH_DEPRECATION_WARNINGS")

    include(${LOG4CPP_TARGETS_FILE})

endif(WIN32)
