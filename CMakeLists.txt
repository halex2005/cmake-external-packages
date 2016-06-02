cmake_minimum_required(VERSION 2.8)

project("cmake-external-packages")

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

add_definitions(-DUNICODE)
add_definitions(-D_UNICODE)

add_subdirectory(protobuf-v2)
add_subdirectory(cppunit)