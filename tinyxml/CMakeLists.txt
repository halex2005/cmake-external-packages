cmake_minimum_required(VERSION 2.8)

option(TINYXML_USE_STL "Build the TinyXml with STL strings" OFF)
set(TINYXML_VERSION_TAG "2.6.2" CACHE STRING "TinyXml version tag from github")
string(REPLACE "." "_" TINYXML_VERSION_TAG_UNDERSCORE ${TINYXML_VERSION_TAG})

set (EXTERNAL_PACKAGES_INCLUDE_DIR ${CMAKE_CURRENT_LIST_DIR}/../include
    CACHE STRING "Directory for third-party include files, where include folders will be copied")

set (TINYXML_ARCHIVE_PATH "${CMAKE_CURRENT_BINARY_DIR}/tinyxml-${TINYXML_VERSION_TAG_UNDERSCORE}.zip")
set (TINYXML_EXTRACT_ARCHIVE_WORKING_DIR ${CMAKE_CURRENT_LIST_DIR})
set (TINYXML_SOURCE_PATH ${TINYXML_EXTRACT_ARCHIVE_WORKING_DIR}/tinyxml/)
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
