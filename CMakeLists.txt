# Copyright (c) Meta Platforms, Inc. and affiliates.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
cmake_minimum_required(VERSION 3.14)

# Set the project name.
project(Nimble)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

add_compile_definitions(DISABLE_META_INTERNAL_COMPRESSOR=1)

# Sets new behavior for CMP0135, which controls how timestamps are extracted
# when using ExternalProject_Add():
# https://cmake.org/cmake/help/latest/policy/CMP0135.html
if(POLICY CMP0135)
  cmake_policy(SET CMP0135 NEW)
  set(CMAKE_POLICY_DEFAULT_CMP0135 NEW)
endif()

list(
  PREPEND
  CMAKE_MODULE_PATH
  "${PROJECT_SOURCE_DIR}/CMake"
)

message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")

# Ignore known compiler warnings.
check_cxx_compiler_flag("-Wstringop-overread" COMPILER_HAS_W_STRINGOP_OVERREAD)
if(COMPILER_HAS_W_STRINGOP_OVERREAD)
  string(APPEND CMAKE_CXX_FLAGS " -Wno-stringop-overread")
endif()

check_cxx_compiler_flag(
  "-Wdeprecated-declarations"
  COMPILER_HAS_W_DEPRECATED_DECLARATIONS
)
if(COMPILER_HAS_W_DEPRECATED_DECLARATIONS)
  string(APPEND CMAKE_CXX_FLAGS " -Wno-deprecated-declarations")
endif()

check_cxx_compiler_flag(
  "-Wmaybe-uninitialized"
  COMPILER_HAS_W_MAYBE_UNINITIALIZED
)
if(COMPILER_HAS_W_MAYBE_UNINITIALIZED)
  string(APPEND CMAKE_CXX_FLAGS " -Wno-maybe-uninitialized")
endif()

check_cxx_compiler_flag(
  "-Wunknown-warning-option"
  COMPILER_HAS_W_UNKNOWN_WARNING_OPTION
)
if(COMPILER_HAS_W_UNKNOWN_WARNING_OPTION)
  string(APPEND CMAKE_CXX_FLAGS " -Wno-unknown-warning-option")
endif()

check_cxx_compiler_flag(
  "-Wnullability-completeness"
  COMPILER_HAS_W_NULLABILITY_COMPLETENESS
)
if(COMPILER_HAS_W_NULLABILITY_COMPLETENESS)
  string(APPEND CMAKE_CXX_FLAGS " -Wno-nullability-completeness")
endif()

message("FINAL CMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}")

find_package(Velox REQUIRED)

include(CTest) # include after project() but before add_subdirectory()

add_subdirectory(dwio/nimble/common)
add_subdirectory(dwio/nimble/tablet)
add_subdirectory(dwio/nimble/tools)
add_subdirectory(dwio/nimble/encodings)
add_subdirectory(dwio/nimble/stats)
add_subdirectory(dwio/nimble/velox)
