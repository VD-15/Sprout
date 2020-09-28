cmake_minimum_required(VERSION 3.10)

if (CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)
	message(FATAL_ERROR "In-source builds are not allowed, please conduct builds from within a separate build directory")
endif()

set(CMAKE_DISABLE_IN_SOURCE_BUILD  ON)
set(CMAKE_DISABLE_SOURCE_CHANGES   ON)
project(sprout)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/CMakeFiles/modules/")

#include(CheckIncludeFiles)
#include (FindPkgConfig)
#include(CheckCXXSourceCompiles)

# build release by default
if( NOT DEFINED CMAKE_BUILD_TYPE )
    set(CMAKE_BUILD_TYPE RELEASE)
endif()

option( BUILD_TESTING "If true Sprout's tests will be built" FALSE)

# avoid use of compiler-specific flags
#set(CMAKE_CXX_FLAGS_DEBUG "-W -Wall -Wextra -Wno-unused-parameter -Werror -std=c++0x -O0 -g")
#set(CMAKE_C_FLAGS_DEBUG "-W -Wall -Wextra -Wno-unused-parameter -Werror -O0 -g")
#set(CMAKE_CXX_FLAGS_RELEASE "-W -Wall -Wextra -Wno-unused-parameter -Werror -std=c++0x -O2")
#set(CMAKE_C_FLAGS_RELEASE "-W -Wall -Wextra -Wno-unused-parameter -Werror -O2")

#if you don't want the full compiler output, remove the following line
#if( NOT DEFINED CMAKE_VERBOSE_MAKEFILE )
#  set(CMAKE_VERBOSE_MAKEFILE OFF)
#endif( NOT DEFINED CMAKE_VERBOSE_MAKEFILE )

if( NOT DEFINED Boost_USE_MULTITHREADED )
    set(Boost_USE_MULTITHREADED ON)
endif( NOT DEFINED Boost_USE_MULTITHREADED )

find_package( Boost 1.49.0 REQUIRED )
if( NOT Boost_FOUND )
    message(FATAL_ERROR "Required package Boost was not detected.")
endif (NOT Boost_FOUND)

#pkg_check_modules(OpenCV opencv)

#INCLUDE_DIRECTORIES( ${CMAKE_CURRENT_SOURCE_DIR} ${Boost_INCLUDE_DIRS} )
#link_directories( ${Boost_LIBRARY_DIRS} )
#subdirs( sprout  tools cmake )

add_library(sprout INTERFACE)
target_include_directories(sprout INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories(sprout INTERFACE ${Boost_INCLUDE_DIRS})
target_link_libraries(sprout INTERFACE ${Boost_LIBRARIES})

if (${CMAKE_PROJECT_NAME} STREQUAL ${PROJECT_NAME})
    set(Sprout_IS_SUBPROJECT FALSE)
else()
    set(Sprout_IS_SUBPROJECT TRUE)
endif()

if (NOT Sprout_IS_SUBPROJECT)
    add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/sprout)
    add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/tools)
    add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/cmake)
    
    if(BUILD_TESTING)
        ENABLE_TESTING()
        include_directories(${CMAKE_CURRENT_SOURCE_DIR})
        add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/libs)
    endif()
endif()


