# Check minimum required CMake version
cmake_minimum_required(VERSION 3.0)

# Set project name (package name, source archive name)
project("drive-duplicator")

# Set script name
set(SCRIPT_NAME "drive_duplicator")

# Check cmake directories
message("Source dir: ${CMAKE_SOURCE_DIR}")
message("Binary dir: ${CMAKE_BINARY_DIR}")

if("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_BINARY_DIR}")
    message(FATAL_ERROR "cmake in-source build are not allowed - please call cmake from 'build' directory")
endif()

# Get script version
execute_process(
    COMMAND ${CMAKE_SOURCE_DIR}/script_version ${CMAKE_SOURCE_DIR}/${SCRIPT_NAME}
    OUTPUT_VARIABLE SCRIPT_VERSION
)
string(REGEX REPLACE "\n$" "" SCRIPT_VERSION "${SCRIPT_VERSION}")

execute_process(
    COMMAND ${CMAKE_SOURCE_DIR}/script_version ${CMAKE_SOURCE_DIR}/${SCRIPT_NAME} major
    OUTPUT_VARIABLE SCRIPT_VERSION_MAJOR
)
string(REGEX REPLACE "\n$" "" SCRIPT_VERSION_MAJOR "${SCRIPT_VERSION_MAJOR}")

execute_process(
    COMMAND ${CMAKE_SOURCE_DIR}/script_version ${CMAKE_SOURCE_DIR}/${SCRIPT_NAME} minor
    OUTPUT_VARIABLE SCRIPT_VERSION_MINOR
)
string(REGEX REPLACE "\n$" "" SCRIPT_VERSION_MINOR "${SCRIPT_VERSION_MINOR}")

execute_process(
    COMMAND ${CMAKE_SOURCE_DIR}/script_version ${CMAKE_SOURCE_DIR}/${SCRIPT_NAME} patch
    OUTPUT_VARIABLE SCRIPT_VERSION_PATCH
)
string(REGEX REPLACE "\n$" "" SCRIPT_VERSION_PATCH "${SCRIPT_VERSION_PATCH}")

message("${SCRIPT_NAME} version: '${SCRIPT_VERSION}'")

# Install section (make install):
install(
    FILES ${SCRIPT_NAME}
    PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ GROUP_EXECUTE GROUP_READ WORLD_EXECUTE WORLD_READ
    DESTINATION /usr/local/sbin
)

# Packaging (make package, make package_source)
set(CPACK_GENERATOR "DEB")

set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Aleksey Polygaev") #required

set(CPACK_DEBIAN_PACKAGE_DEPENDS "util-linux,coreutils,gawk,sed,grep")

set(CPACK_PACKAGE_VERSION ${SCRIPT_VERSION})
set(CPACK_PACKAGE_VERSION_MAJOR ${SCRIPT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${SCRIPT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${SCRIPT_VERSION_PATCH})

set(CPACK_DEBIAN_FILE_NAME "${SCRIPT_NAME}-${SCRIPT_VERSION}.deb")

include(CPack)
