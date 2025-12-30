cmake_minimum_required(VERSION 4.0)

set(LUA_VERSION 5.5.0)

set(TARGET lua)

project(${TARGET} LANGUAGES C)

file(GLOB LUA_SRC lua-${LUA_VERSION}/src/*.c)
file(GLOB LUA_INC lua-${LUA_VERSION}/src/*.h)
list(FILTER LUA_SRC EXCLUDE REGEX "lua.c|luac.c$")

add_library(${TARGET} ${LUA_INC} ${LUA_SRC})

target_include_directories (${TARGET} PRIVATE lua-${LUA_VERSION})

#if (WIN32)

    install(TARGETS ${TARGET}
    EXPORT ${TARGET}Targets
    FILE_SET HEADERS
    LIBRARY DESTINATION lib/
    ARCHIVE DESTINATION lib/
    RUNTIME DESTINATION bin/
    INCLUDES DESTINATION include/)

    include(CMakePackageConfigHelpers)

    write_basic_package_version_file(
        "${TARGET}ConfigVersion.cmake"
        VERSION ${LUA_VERSION}
        COMPATIBILITY AnyNewerVersion)

    install(EXPORT ${TARGET}Targets
    FILE ${TARGET}Targets.cmake
    DESTINATION lib/cmake/${TARGET})

    install(FILES "${TARGET}Config.cmake" "${CMAKE_CURRENT_BINARY_DIR}/${TARGET}ConfigVersion.cmake"
        DESTINATION sdk/lib/cmake/${TARGET})

    install(FILES ${LUA_INC} DESTINATION include/)

#else()
