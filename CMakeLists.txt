cmake_minimum_required(VERSION 4.0)
project(lua LANGUAGES C)

# Версия Lua
set(LUA_VERSION 5.4.8)
set(LUA_SOVERSION 5.4)

# Ищем исходники Lua (можно в поддиректории или как внешний проект)
set(LUA_PATH lua-${LUA_VERSION}/src)
file(GLOB LUA_SRCS ${LUA_PATH}/*.c)

# исключаем исполняемые
get_filename_component(PATH_LUA_C ${LUA_PATH}/lua.c ABSOLUTE)
get_filename_component(PATH_LUAC_C ${LUA_PATH}/luac.c ABSOLUTE)
list(REMOVE_ITEM LUA_SRCS ${PATH_LUA_C} ${PATH_LUAC_C})

add_library(lua ${LUA_SRCS})
add_library(lua::lua ALIAS lua)

target_include_directories(lua PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lua-${LUA_VERSION}/src>
    $<INSTALL_INTERFACE:include>
)

set(LUA_PUBLIC_HEADERS
    lua-${LUA_VERSION}/src/lua.h
    lua-${LUA_VERSION}/src/lualib.h
    lua-${LUA_VERSION}/src/lauxlib.h
    lua-${LUA_VERSION}/src/luaconf.h
)

set_target_properties(lua PROPERTIES
    VERSION ${LUA_VERSION}
    SOVERSION ${LUA_SOVERSION}
    PUBLIC_HEADER "${LUA_PUBLIC_HEADERS}"
)

# Установка
include(GNUInstallDirs)

install(TARGETS lua
    EXPORT luaTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(EXPORT luaTargets
    FILE luaTargets.cmake
    NAMESPACE lua::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/lua
)

# Конфигурационный файл для find_package
include(CMakePackageConfigHelpers)

configure_package_config_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/luaConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/luaConfig.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/lua
)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/luaConfigVersion.cmake
    VERSION ${LUA_VERSION}
    COMPATIBILITY SameMajorVersion
)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/luaConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/luaConfigVersion.cmake
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/lua
)
