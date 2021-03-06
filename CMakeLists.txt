cmake_minimum_required(VERSION 3.10)  # bionic's cmake version

find_program(CCACHE_PROGRAM ccache)
if(CCACHE_PROGRAM)
  foreach(lang C CXX)
    if(NOT DEFINED CMAKE_${lang}_COMPILER_LAUNCHER AND NOT CMAKE_${lang}_COMPILER MATCHES ".*/ccache")
      message(STATUS "Enabling ccache for ${lang}")
      set(CMAKE_${lang}_COMPILER_LAUNCHER ${CCACHE_PROGRAM} CACHE STRING "")
    endif()
  endforeach()
endif()

set(PROJECT_NAME loki-whois)
project(${PROJECT_NAME}
    VERSION 0.0.1
    DESCRIPTION "loki-whois - whois server for loki blockchain"
    LANGUAGES CXX)

include(CheckCXXSourceCompiles)
include(CheckLibraryExists)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

find_package(PkgConfig)
pkg_check_modules(PC_OXENMQ liboxenmq REQUIRED)
pkg_check_modules(PC_SODIUM libsodium REQUIRED)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON) 

set(BUILD_TESTING OFF)
set(BUILD_UVW_LIBS ON)
add_subdirectory(external/uvw)

add_executable(${PROJECT_NAME} whois.cpp)
target_link_libraries(${PROJECT_NAME} PUBLIC ${PC_OXENMQ_LIBRARIES} uvw::uvw-static ${PC_SODIUM_LIBRARIES})
