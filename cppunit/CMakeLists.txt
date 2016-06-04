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
#     will create libcppunit cmake target
#     use `target_link_libraries(your_exe libcppunit)` to add reference to libcppunit target
# Customization:
#     Only lastest version 1.12.1 is supported for now.
#     CPPUNIT_BUILD_SHARED_LIBS ON/OFF - option for build the cppunit as shared/static libraries
#     EXTERNAL_PACKAGES_INCLUDE_DIR - directory where cppunit interface includes will be copied.
#         You should add this directory to include_directories()
#=============================================================================
cmake_minimum_required(VERSION 2.8)

if (WIN32)

    option(CPPUNIT_BUILD_SHARED_LIBS "Build the cppunit as shared library" OFF)

    set (EXTERNAL_PACKAGES_INCLUDE_DIR ${CMAKE_CURRENT_LIST_DIR}/../include
        CACHE STRING "Directory for third-party include files, where include folders will be copied")

    set (CPPUNIT_ARCHIVE_PATH ${CMAKE_CURRENT_BINARY_DIR}/cppunit-1.12.1.tar.gz)
    set (EXTRACT_ARCHIVE_WORKING_DIR ${CMAKE_CURRENT_LIST_DIR})
    set (CPPUNIT_SOURCE_PATH ${CMAKE_CURRENT_LIST_DIR}/cppunit-1.12.1/)
    set (CPPUNIT_INCLUDE_PATH ${EXTERNAL_PACKAGES_INCLUDE_DIR}/cppunit/)

    if (NOT EXISTS ${CPPUNIT_SOURCE_PATH}/src)
        if (NOT EXISTS ${CPPUNIT_ARCHIVE_PATH})
            file(DOWNLOAD
                http://downloads.sourceforge.net/project/cppunit/cppunit/1.12.1/cppunit-1.12.1.tar.gz
                ${CPPUNIT_ARCHIVE_PATH}
                SHOW_PROGRESS)
        endif()
        execute_process(
            COMMAND ${CMAKE_COMMAND} -E tar xvf ${CPPUNIT_ARCHIVE_PATH}
            WORKING_DIRECTORY ${EXTRACT_ARCHIVE_WORKING_DIR})
        if (NOT EXISTS ${CPPUNIT_SOURCE_PATH}/src)
            message(FATAL_ERROR "Please, check where cppunit was extracted")
        endif()
    endif()

    if (NOT EXISTS ${CPPUNIT_INCLUDE_PATH})
        file(COPY "${CPPUNIT_SOURCE_PATH}/include/cppunit" DESTINATION "${EXTERNAL_PACKAGES_INCLUDE_DIR}")
        if (NOT EXISTS ${CPPUNIT_INCLUDE_PATH})
            message(FATAL_ERROR "Please, check where cppunit include was copied")
        endif()
    endif()

    include_directories("${CPPUNIT_SOURCE_PATH}/include")

    SET(CPPUNIT_HEADERS
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/AdditionalMessage.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/Asserter.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/BriefTestProgressListener.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/CompilerOutputter.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/config/config-bcb5.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/config/config-evc4.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/config/config-mac.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/config/config-msvc6.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/config/CppUnitApi.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/config/SelectDllLoader.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/config/SourcePrefix.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/Exception.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/AutoRegisterSuite.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/ExceptionTestCaseDecorator.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/HelperMacros.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/Orthodox.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/RepeatedTest.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestCaseDecorator.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestDecorator.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestFactory.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestFactoryRegistry.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestFixtureFactory.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestNamer.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestSetUp.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestSuiteBuilderContext.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TestSuiteFactory.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/extensions/TypeInfoHelper.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/Message.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/Outputter.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/plugin/DynamicLibraryManager.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/plugin/DynamicLibraryManagerException.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/plugin/PlugInManager.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/plugin/PlugInParameters.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/plugin/TestPlugIn.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/plugin/TestPlugInDefaultImpl.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/Portability.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/portability/CppUnitDeque.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/portability/CppUnitMap.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/portability/CppUnitSet.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/portability/CppUnitStack.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/portability/CppUnitVector.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/portability/FloatingPoint.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/portability/Stream.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/Protector.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/SourceLine.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/SynchronizedObject.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/Test.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestAssert.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestCaller.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestCase.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestComposite.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestFailure.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestFixture.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestLeaf.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestListener.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestPath.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestResult.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestResultCollector.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestRunner.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TestSuite.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TextOutputter.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TextTestProgressListener.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TextTestResult.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/TextTestRunner.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/tools/Algorithm.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/tools/StringTools.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/tools/XmlDocument.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/tools/XmlElement.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/ui/text/TestRunner.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/ui/text/TextTestRunner.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/XmlOutputter.h"
        "${CPPUNIT_SOURCE_PATH}/include/cppunit/XmlOutputterHook.h"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/DefaultProtector.h"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/ProtectorChain.h"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/ProtectorContext.h"
    )

    set (CPPUNIT_SOURCES
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/AdditionalMessage.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/Asserter.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/BeOsDynamicLibraryManager.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/BriefTestProgressListener.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/CompilerOutputter.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/DefaultProtector.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/DynamicLibraryManager.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/DynamicLibraryManagerException.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/Exception.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/Message.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/PlugInManager.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/PlugInParameters.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/Protector.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/ProtectorChain.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/RepeatedTest.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/ShlDynamicLibraryManager.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/SourceLine.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/StringTools.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/SynchronizedObject.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/Test.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestAssert.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestCase.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestCaseDecorator.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestComposite.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestDecorator.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestFactoryRegistry.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestFailure.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestLeaf.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestNamer.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestPath.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestPlugInDefaultImpl.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestResult.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestResultCollector.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestRunner.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestSetUp.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestSuccessListener.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestSuite.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TestSuiteBuilderContext.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TextOutputter.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TextTestProgressListener.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TextTestResult.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TextTestRunner.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/TypeInfoHelper.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/UnixDynamicLibraryManager.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/Win32DynamicLibraryManager.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/XmlDocument.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/XmlElement.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/XmlOutputter.cpp"
        "${CPPUNIT_SOURCE_PATH}/src/cppunit/XmlOutputterHook.cpp"
    )

    if (CPPUNIT_BUILD_SHARED_LIBS)
        add_library(libcppunit SHARED
            ${CPPUNIT_HEADERS}
            ${CPPUNIT_SOURCES}
            ${CPPUNIT_SOURCE_PATH}/src/cppunit/DllMain.cpp
        )
        target_compile_definitions(libcppunit PUBLIC CPPUNIT_BUILD_DLL)
    else()
        add_library(libcppunit STATIC ${CPPUNIT_HEADERS} ${CPPUNIT_SOURCES})
    endif()

endif(WIN32)