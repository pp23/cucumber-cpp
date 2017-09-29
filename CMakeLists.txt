cmake_minimum_required(VERSION 2.8.12)

project(${MODULE_NAME})

set(CUKE_USE_STATIC_BOOST ${WIN32} CACHE BOOL "Statically link Boost (except boost::test)")
set(CUKE_USE_STATIC_GTEST ON CACHE BOOL "Statically link Google Test")

set(CUKE_DISABLE_BOOST_TEST OFF CACHE BOOL "Disable boost:test")
set(CUKE_DISABLE_GTEST OFF CACHE BOOL "Disable Google Test framework")
set(CUKE_DISABLE_UNIT_TESTS OFF CACHE BOOL "Disable unit tests")
set(CUKE_DISABLE_E2E_TESTS OFF CACHE BOOL "Disable end-to-end tests")
set(CUKE_ENABLE_EXAMPLES OFF CACHE BOOL "Enable the examples")
set(GMOCK_SRC_DIR "" CACHE STRING "Google Mock framework sources path (otherwise downloaded)")
set(GMOCK_VER "1.7.0" CACHE STRING "Google Mock framework version to be used")
option(VALGRIND_TESTS "Run tests within Valgrind" OFF)

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/modules)

#
# Generic Compiler Flags
#

if(CMAKE_COMPILER_IS_GNUCXX)
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Weffc++")
    # TODO: A better fix should handle ld's --as-needed flag
    if(UNIX)
        set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Xlinker '--no-as-needed'")
    endif()
elseif(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DNOMINMAX") # exclude M$ min/max macros
    #set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /analyze")
endif()

#module_dependencies(PUBLIC ext.boost.thread ext.boost.system ext.boost.regex ext.boost.date.time ext.boost.program.options ext.boost.filesystem)

set(CUKE_EXTRA_LIBRARIES ${CUKE_EXTRA_LIBRARIES} ${Boost_THREAD_LIBRARY} ${Boost_SYSTEM_LIBRARY} ${Boost_REGEX_LIBRARY} ${Boost_DATE_TIME_LIBRARY} ${Boost_PROGRAM_OPTIONS_LIBRARY} ${Boost_FILESYSTEM_LIBRARY})

#
# GTest
#

if(NOT CUKE_DISABLE_GTEST)
    set(GTEST_USE_STATIC_LIBS ${CUKE_USE_STATIC_GTEST})
    if(NOT GMOCK_ROOT)
        set(GMOCK_ROOT "${CMAKE_CURRENT_BINARY_DIR}/gmock")
    endif()
    find_package(GMock REQUIRED)

    if(GTEST_FOUND)
        set(CUKE_GTEST_LIBRARIES
            GTest::GTest
            )
    endif()
    if(GMOCK_FOUND)
        set(CUKE_GMOCK_LIBRARIES
            GTest::GTest
            GMock::GMock
            GMock::Main
            )
    endif()
endif()

#
# Cucumber-Cpp
#

set(CUKE_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)

include_directories(${CUKE_INCLUDE_DIR})

add_subdirectory(src)

module_include_directories(INTERFACE "${CUKE_INCLUDE_DIR}")
