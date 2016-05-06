cmake_minimum_required(VERSION 2.8)
project(RSBench-ompss C)

#version number
set(RSBench-ompss_VERSION_MAJOR 1)
set(RSBench-ompss_VERSION_MINOR 0)

option(OPTIMIZE "Optimize"              ON)
option(DEBUG    "Debug information"     OFF)
option(PROFILE  "Compile for profiling" OFF)
option(STATUS   "Compile with status"   ON)
option(PAPI     "Compile with PAPI lib" OFF)

if (DEBUG OR PROFILE)
    set(OPTIMIZE OFF)
endif (DEBUG OR PROFILE)

set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${CMAKE_SOURCE_DIR}/cmake")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")

find_package(OpenMP)
if (OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
endif(OPENMP_FOUND)

if (OPTIMIZE)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -march=native")
elseif (DEBUG)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O0 -g")
elseif (PROFILE)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O0 -g -pg")
endif ()

if (STATUS)
    add_definitions(-DSTATUS)
endif (STATUS)

if (PAPI)
    find_package(PAPI REQUIRED)
    message(STATUS "PAPI_INCLUDE_DIRS ${PAPI_INCLUDE_DIRS}")
    message(STATUS "PAPI_LIBRARIES    ${PAPI_LIBRARIES}")

    add_definitions(-DPAPI)
    include_directories(${PAPI_INCLUDE_DIRS})
endif (PAPI)

if ("${CMAKE_C_COMPILER_ID}" STREQUAL "GNU" OR
    "${CMAKE_C_COMPILER_ID}" STREQUAL "Clang")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -ffast-math")
elseif ("${CMAKE_C_COMPILER_ID}" STREQUAL "Intel")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -xhost -ansi-alias -no-prec-div")
elseif ("${CMAKE_C_COMPILER_ID}" STREQUAL "PGI")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -mp -fastsse")
endif()
    
add_subdirectory(src)
