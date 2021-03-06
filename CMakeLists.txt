#
# CMakeLists.txt - CMake configuration file for swmm-solver
#
# Created: July 11, 2019
# Updated: May 29, 2020
#
# Author: Michael E. Tryby
#         US EPA ORD/CESER
#


cmake_minimum_required (VERSION 3.13)

if("${CMAKE_BINARY_DIR}" STREQUAL "${CMAKE_SOURCE_DIR}")
    message(FATAL_ERROR "In-source builds are disabled.")
endif()


project(swmm-solver
    VERSION 5.1.14
    LANGUAGES C CXX
    )

# Append local dir to module search path
# list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

# Sets the position independent code property for all targets
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Sets default install prefix when cmakecache is initialized for first time
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX ${CMAKE_BINARY_DIR}/install CACHE PATH "..." FORCE)
endif()


# Define install locations (will be prepended by install prefix)
set(TOOL_DIST "bin")
set(INCLUDE_DIST "include")
set(LIBRARY_DIST "lib")
set(CONFIG_DIST  "cmake")


option(BUILD_TESTS "Build component tests (requires Boost)" OFF)


# Add project subdirectories
add_subdirectory(src/outfile)
add_subdirectory(src/solver)
add_subdirectory(src/run)

if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()


# Create target import scripts so other cmake projects can use swmm libraries
install(
    EXPORT
        swmm5Targets
    DESTINATION
        "${CONFIG_DIST}"
    FILE
        swmm5-config.cmake
    )

install(
    EXPORT
        swmm-outputTargets
    DESTINATION
        "${CONFIG_DIST}"
    FILE
        swmm-output-config.cmake
    )


# Create install rules for vcruntime.dll, msvcp.dll, vcomp.dll etc.
set(CMAKE_INSTALL_OPENMP_LIBRARIES TRUE)
include(InstallRequiredSystemLibraries)


# Configure CPack driven installer package
set(CPACK_GENERATOR "ZIP")
set(CPACK_PACKAGE_VENDOR "US_EPA")
set(CPACK_ARCHIVE_FILE_NAME "swmm")

include(CPack)
