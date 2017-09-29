set(CUKE_SOURCES
    drivers/GenericDriver.cpp
    ContextManager.cpp
    CukeCommands.cpp
    CukeEngine.cpp
    CukeEngineImpl.cpp
    StepManager.cpp
    HookRegistrar.cpp
    Regex.cpp
    Scenario.cpp
    Table.cpp
    Tag.cpp
    connectors/wire/WireServer.cpp
    connectors/wire/WireProtocol.cpp
    connectors/wire/WireProtocolCommands.cpp
    )

if(GTEST_FOUND)
    list(APPEND CUKE_DEP_LIBRARIES GTest::GTest)
    list(APPEND CUKE_SOURCES drivers/GTestDriver.cpp)
endif()

if(CMAKE_EXTRA_GENERATOR OR MSVC_IDE)
    message(STATUS "Adding header files to project")
    file(GLOB_RECURSE CUKE_HEADERS "${CUKE_INCLUDE_DIR}/cucumber-cpp/*.hpp")
    if(MSVC_IDE)
        source_group("Header Files" FILES ${CUKE_HEADERS})
    endif()
    list(APPEND CUKE_SOURCES ${CUKE_HEADERS})
endif()

#add_library(cucumber-cpp-nomain STATIC ${CUKE_SOURCES})
BUILD_ARTIFACT(lib-shared ${CUKE_SOURCES} main.cpp)
#add_library(cucumber-cpp STATIC ${CUKE_SOURCES} main.cpp)

module_dependencies(PUBLIC ext.google.mock ext.cppunit ext.boost.thread ext.boost.system ext.boost.regex ext.boost.date.time ext.boost.program.options ext.boost.filesystem)
#target_link_libraries(cucumber-cpp-nomain PRIVATE ${CUKE_DEP_LIBRARIES})
#target_link_libraries(cucumber-cpp PRIVATE ${CUKE_DEP_LIBRARIES})
