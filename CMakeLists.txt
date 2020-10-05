cmake_minimum_required(VERSION 3.12)

# MSVC runtime library flags are selected by an abstraction.
cmake_policy(SET CMP0091 NEW)

option(MESHCONV_USE_MSVC_STATIC_RUNTIME "Use MSVC static runtime library" ON)

if(MESHCONV_USE_MSVC_STATIC_RUNTIME)
    message(STATUS "Using MSVC static runtime")
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()

project(meshconvAll)

# In source build are not allowed
if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})
    message(FATAL_ERROR "In-source builds not allowed. \ 
            Please make a new directory (called a build directory) and run CMake from there. You may need to remove CMakeCache.txt.")
endif()

if((CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT))
    set(CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/install" CACHE PATH "Prefix prepended to install directories" FORCE)
    message(STATUS "Setting default CMAKE_INSTALL_PREFIX to: ${CMAKE_INSTALL_PREFIX}")
endif()

include(GNUInstallDirs)

if( WIN32 AND NOT MESHCONV_USE_MSVC_STATIC_RUNTIME )
  # To avoid having to separately install dependencies such as the VC++ redistributables,
  # we embed the compiler-provided system runtime libraries (vcruntime DLL, api-mw-win-* DLLs, ...)
  # (not putting this in a if(MSVC) because it could theoretically be useful in other contexts)
  set(CMAKE_INSTALL_SYSTEM_RUNTIME_LIBS_SKIP FALSE)
  set(CMAKE_INSTALL_UCRT_LIBRARIES TRUE)
  set(CMAKE_INSTALL_SYSTEM_RUNTIME_DESTINATION ${CMAKE_INSTALL_BINDIR})
  include(InstallRequiredSystemLibraries)
endif()# Set the output directories globally

set(CMAKE_DEBUG_POSTFIX "_d")

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

list(APPEND CMAKE_PREFIX_PATH ${CMAKE_BINARY_DIR})

add_subdirectory(miniFlowVR)

add_subdirectory(meshconv)

