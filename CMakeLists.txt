#
# Main cmake file.
#
# Part of https://github.com/ManelJimeno/cpp-binginds-for-gstreamer (C) 2022
#
# Authors: Manel Jimeno <manel.jimeno@gmail.com>
#
# License: http://www.opensource.org/licenses/mit-license.php MIT
#

cmake_minimum_required(VERSION 3.22.3-3.23)
# Common values
set(PROJECT_NAME, "gstreamer_cpp")
set(PROJECT_DESCRIPTION, "c++17 bindings for GStreamer")
set(LIBRARY_NAME, "gstreamer_cpp")
set(COPYRIGHT_PROJECT, "2022, Manel J")
set(AUTHOR_PROJECT, "Manel J.")

# Load required programs
find_package(Python3 "3.8" QUIET REQUIRED)

# Add cmake-tools
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/tools/cmake")
include(cmake-tools)

# Project definition
project(
    ${PROJECT_NAME}
    VERSION "${PRODUCT_VERSION}"
    DESCRIPTION "${PROJECT_DESCRIPTION}"
    LANGUAGES CXX)

# Options
option(BUILD_UNIT_TESTS "Build unit tests" ON)

# Conan settings
set(CONAN_PACKAGES)

if(LINUX)
    list(APPEND CONAN_PACKAGES expat/2.5.0)
endif()

set(CONAN_PACKAGES_TO_FIND)
set(CONAN_BUILD_OPTIONS *:shared=True)
set(CONAN_BUILD_MISSING ON)

if(BUILD_UNIT_TESTS)
    list(APPEND CONAN_PACKAGES gtest/cci.20210126)
    list(APPEND CONAN_PACKAGES_TO_FIND GTest)
endif()

conan_configure(REQUIRES ${CONAN_PACKAGES} OPTIONS ${CONAN_BUILD_OPTIONS} FIND_PACKAGES ${CONAN_PACKAGES_TO_FIND})

set(CUSTOM_ENVIRONMENT "DYLD_LIBRARY_PATH=\"${CMAKE_BINARY_DIR}/lib\"" "LD_LIBRARY_PATH=\"${CMAKE_BINARY_DIR}/lib\"")

write_conan_environment_file(OUTPUT_FOLDER "${CMAKE_BINARY_DIR}" SET ${CONFIG_CUSTOM_ENVIRONMENT})

# Create targets
add_subdirectory(src)
