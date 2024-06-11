cmake_minimum_required(VERSION 3.10)
project(CUDD
  VERSION 3.0.0
  DESCRIPTION "CUDD: University of Colorado at Boulder Decision Diagrams"
  HOMEPAGE_URL "https://ssoelvsten.github.io/cudd/"
  LANGUAGES C CXX
)

# ============================================================================ #
# Settings
# ============================================================================ #

message(STATUS "")

message(STATUS "CMake build configuration for CUDD ${PROJECT_VERSION}")

message(STATUS "  OS:                         ${CMAKE_SYSTEM_NAME}")
message(STATUS "  Compiler:                   ${CMAKE_CXX_COMPILER_ID} ${CMAKE_CXX_COMPILER_VERSION}")
message(STATUS "  |  C Flags:                 ${CMAKE_C_FLAGS}")
message(STATUS "  |  CXX Flags:               ${CMAKE_CXX_FLAGS}")
message(STATUS "  |  EXE Linker Flags:        ${CMAKE_EXE_LINKER_FLAGS}")

message(STATUS "  Library Options:")

option(CUDD_CPP "Build C++ API" ON)
message(STATUS "  |  Build C++ API:           ${CUDD_CPP}")

option(CUDD_SHARED "Build CUDD as a shared library" OFF)
message(STATUS "  |  Shared:                  ${CUDD_SHARED}")

option(CUDD_STATS "Build CUDD with statistics" OFF)
message(STATUS "  |  Statistics:              ${CUDD_STATS}")

option(CUDD_NANOTRAV "Build Nanotrav Example" OFF)
message(STATUS "  |  Build Nanotrav:          ${CUDD_NANOTRAV}")

message(STATUS "")

if(CUDD_CPP)
  include(CheckCXXCompilerFlag)
  CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
  if(NOT COMPILER_SUPPORTS_CXX11)
    message(FATAL_ERROR "${CMAKE_CXX_COMPILER} has no C++11 support.")
  endif()
  include(CheckCXXSourceCompiles)
  set(HAVE_MODERN_CXX TRUE)
  set(HAVE_WORKING_THREAD TRUE)
endif()

# ============================================================================ #
# Compiler Settings
# ============================================================================ #

include(CheckCXXCompilerFlag)

if(CUDD_WITH_SYSTEM_QSORT)
  set(USE_SYSTEM_QSORT TRUE)
endif()

# set(__USE_MINGW_ANSI_STDIO ON) # TODO: Test.

find_package(Threads)
if(CMAKE_USE_PTHREADS_INIT)
  add_definitions(-DHAVE_PTHREADS=1)
  set(HAVE_PTHREADS TRUE)
  add_definitions("-pthread") # TODO: Revise per target.
  # target_link_libraries(${name} ${CMAKE_THREAD_LIBS_INIT})
endif()

include(CheckIncludeFile)

CHECK_INCLUDE_FILE("float.h" HAVE_FLOAT_H)
if(NOT (HAVE_FLOAT_H))
  message(FATAL_ERROR "'float.h' missing.")
endif()

CHECK_INCLUDE_FILE("inttypes.h" HAVE_INTTYPES_H)
if(NOT (HAVE_INTTYPES_H))
  message(FATAL_ERROR "'inttypes.h' missing.")
endif()

CHECK_INCLUDE_FILE("limits.h" HAVE_LIMITS_H)
if(NOT (HAVE_LIMITS_H))
  message(FATAL_ERROR "'limits.h' missing.")
endif()

CHECK_INCLUDE_FILE("stddef.h" HAVE_STDDEF_H)
if(NOT (HAVE_STDDEF_H))
  message(FATAL_ERROR "'stddef.h' missing.")
endif()

CHECK_INCLUDE_FILE("stdlib.h" HAVE_STDLIB_H)
if(NOT (HAVE_STDLIB_H))
  message(FATAL_ERROR "'stdlib.h' missing.")
endif()

CHECK_INCLUDE_FILE("string.h" HAVE_STRING_H)
if(NOT (HAVE_STRING_H))
  message(FATAL_ERROR "'string.h' missing.")
endif()

CHECK_INCLUDE_FILE("assert.h" HAVE_ASSERT_H)
if(NOT (HAVE_ASSERT_H))
  message(FATAL_ERROR "'assert.h' missing.")
endif()

CHECK_INCLUDE_FILE("math.h" HAVE_MATH_H)
if(NOT (HAVE_MATH_H))
  message(FATAL_ERROR "'math.h' missing.")
endif()

CHECK_INCLUDE_FILE("unistd.h" HAVE_UNISTD_H)
CHECK_INCLUDE_FILE("sys/time.h" HAVE_SYS_TIME_H)
CHECK_INCLUDE_FILE("sys/times.h" HAVE_SYS_TIMES_H)
CHECK_INCLUDE_FILE("sys/resource.h" HAVE_SYS_RESOURCE_H)
CHECK_INCLUDE_FILE("sys/wait.h" HAVE_SYS_WAIT_H)
CHECK_INCLUDE_FILE("sys/stat.h" HAVE_SYS_STAT_H)
CHECK_INCLUDE_FILE("dlfcn.h" HAVE_DLFCN_H) # libtool
CHECK_INCLUDE_FILE("memory.h" HAVE_MEMORY_H)
CHECK_INCLUDE_FILE("strings.h" HAVE_STRINGS_H)

set(STDC_HEADERS TRUE) # TODO: Test

CHECK_INCLUDE_FILE("stdbool.h" HAVE__BOOL)

include(CheckTypeSize)
CHECK_TYPE_SIZE(size_t SIZE_T)
CHECK_TYPE_SIZE(uint16_t UINT16_T)
CHECK_TYPE_SIZE(uint32_t UINT32_T)
CHECK_TYPE_SIZE(ptrdiff_t PTRDIFF_T)
CHECK_TYPE_SIZE(int SIZEOF_INT)
CHECK_TYPE_SIZE(long SIZEOF_LONG)
CHECK_TYPE_SIZE("void*" SIZEOF_VOID_P)
CHECK_TYPE_SIZE("long double" SIZEOF_LONG_DOUBLE)

include(CheckFunctionExists)

set(CMAKE_REQUIRED_INCLUDES "math.h")
set(CMAKE_REQUIRED_LIBRARIES m)

CHECK_FUNCTION_EXISTS(pow HAVE_POW)
if(NOT (HAVE_POW))
  message(FATAL_ERROR "'pow' function missing.")
endif()

CHECK_FUNCTION_EXISTS(sqrt HAVE_SQRT)
if(NOT (HAVE_SQRT))
  message(FATAL_ERROR "'sqrt' function missing.")
endif()

CHECK_FUNCTION_EXISTS(strchr HAVE_STRCHR)
if(NOT (HAVE_STRCHR))
  message(FATAL_ERROR "'strchr' function missing.")
endif()

CHECK_FUNCTION_EXISTS(strstr HAVE_STRSTR)
if(NOT (HAVE_STRSTR))
  message(FATAL_ERROR "'strstr' function missing.")
endif()

CHECK_FUNCTION_EXISTS(powl HAVE_POWL)
CHECK_FUNCTION_EXISTS(gethostname HAVE_GETHOSTNAME)
CHECK_FUNCTION_EXISTS(getrlimit HAVE_GETRLIMIT)
CHECK_FUNCTION_EXISTS(getrusage HAVE_GETRUSAGE)
CHECK_FUNCTION_EXISTS(sysconf HAVE_SYSCONF)

include(CheckCSourceCompiles)
CHECK_C_SOURCE_COMPILES(
  "#include <math.h>
  int main() { double x = INFINITY; }
  " HAVE_IEEE_754)

# ============================================================================ #
# Library
# ============================================================================ #

set(CUDD_SOURCE_DIR    "${CMAKE_CURRENT_SOURCE_DIR}/cudd")
set(CUDD_CPP_DIR       "${CMAKE_CURRENT_SOURCE_DIR}/cplusplus")
set(CUDD_UTIL_DIR      "${CMAKE_CURRENT_SOURCE_DIR}/util")
set(CUDD_EPD_DIR       "${CMAKE_CURRENT_SOURCE_DIR}/epd")
set(CUDD_MTR_DIR       "${CMAKE_CURRENT_SOURCE_DIR}/mtr")
set(CUDD_ST_DIR        "${CMAKE_CURRENT_SOURCE_DIR}/st")

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.in
               ${CMAKE_CURRENT_BINARY_DIR}/config.h)

include_directories("${CMAKE_CURRENT_BINARY_DIR}")

add_subdirectory(util)
add_subdirectory(epd)
add_subdirectory(st)
add_subdirectory(mtr)

add_subdirectory(cudd)

# ============================================================================ #
# DDDMP
# ============================================================================ #
add_subdirectory(dddmp)

# ============================================================================ #
# Nanotrav
# ============================================================================ #
add_subdirectory(nanotrav)

# ============================================================================ #
# Doxygen documentation
# ============================================================================ #

find_package(Doxygen)

if(DOXYGEN)
  # configure doxygen files
  set(DOXYGEN_IN ${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in)
  set(DOXYGEN_OUT ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)

  configure_file(${DOXYGEN_IN} ${DOXYGEN_OUT} @ONLY)

  # add target
  add_custom_target(cudd-doc
    COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYGEN_OUT}
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    COMMENT "Generating API documentation with Doxygen"
    VERBATIM)
else(DOXYGEN)
  message(STATUS "Doxygen not found, CUDD API documentation cannot be generated.")
endif(DOXYGEN)
