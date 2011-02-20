# This CMakeLists.txt has been tested with bzip2 version 1.0.6
# using Microsoft Visual Studio 2008 Express under Windows Vista
# and cmake 2.8

cmake_minimum_required(VERSION 2.6)

# Set build type to Debug by default
if(DEFINED CMAKE_BUILD_TYPE)
   set(CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE} CACHE STRING "Choose the type of build, options are: None(CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel.")
else()
   set(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build, options are: None(CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel.")
endif()

# Define bzip2 project
project(bzip2 C)

# Include from current directory
set(CMAKE_INCLUDE_CURRENT_DIR ON)

# For future expansion, define public header(s) as a variable
set(BZIP2_PUBLIC_HEADERS bzlib.h)

# libbz2 sources
set(LIBBZ2_SOURCES blocksort.c bzlib.c bzlib.h bzlib_private.h compress.c crctable.c decompress.c huffman.c randtable.c)

# bzip2 sources
set(BZIP2_SOURCES bzip2.c)

# bzip2recover sources
set(BZIP2RECOVER_SOURCES bzip2recover.c)

# If compiling in Windows, using Microsoft Visual C, define these to suppress warnings
if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS -D_CRT_NONSTDC_NO_WARNINGS)
endif(MSVC)

# Under Cygwin, these definitions must be set.
if(CYGWIN)
    if(WIN32)
        add_definitions(-DBZ_UNIX=0 -DBZ_LCCWIN32=1)
    elseif(UNIX)
        add_definitions(-DBZ_UNIX=1 -DBZ_LCCWIN32=0)
    endif(WIN32)
endif(CYGWIN)

# Add bz2 library
add_library(bz2 ${LIBBZ2_SOURCES})

# Add bzip2 executable, add depedency to bz2 and link against it
add_executable(bzip2 ${BZIP2_SOURCES})
add_dependencies(bzip2 bz2)
target_link_libraries(bzip2 bz2)

# Add bzip2recover
add_executable(bzip2recover ${BZIP2RECOVER_SOURCES})

# Set up install for makefile
install(TARGETS bz2 bzip2 bzip2recover
            RUNTIME DESTINATION bin
            LIBRARY DESTINATION lib
            ARCHIVE DESTINATION lib/static)
install(FILES ${BZIP2_PUBLIC_HEADERS} DESTINATION include/bzip2)

