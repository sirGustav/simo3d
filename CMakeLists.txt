cmake_minimum_required(VERSION 3.1.0)
set_property(GLOBAL PROPERTY USE_FOLDERS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
project(simo3d)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH}
    "${PROJECT_SOURCE_DIR}/cmake-modules")

option(BUILD_USE_CLANG_FORMAT "Add clang format to the build" OFF)
option(BUILD_USE_CPPLINT "Add cpplint to the build" OFF)
option(BUILD_USE_DOXYGEN "Add doxygen to the build" OFF)

find_package(AssImp REQUIRED)
find_package(OpenGL REQUIRED)

find_package(Protobuf REQUIRED)
include_directories(${PROTOBUF_INCLUDE_DIRS})

# hack ot tell wxwidgets we have full compiler support
# ADD_DEFINITIONS(/DHAVE_TYPE_TRAITS)
# ADD_DEFINITIONS(/DHAVE_TR1_TYPE_TRAITS)

if(${APPLE})
  # disabling http://stackoverflow.com/questions/32626171/xcode-7-how-to-suppress-warning-overrides-a-member-function-but-is-not-marked
  # that apple-clang spews out for wx headers
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-inconsistent-missing-override")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-declarations")
endif()

# compiler flags
if(${MSVC})
	add_definitions(/D _CRT_SECURE_NO_WARNINGS)

	# character set to Not Set=ASCII/SBCS
  # http://public.kitware.com/Bug/view.php?id=12189
	add_definitions(/D _SBCS)
endif()

# force visual studio and the like to only have debug and release builds
if(CMAKE_CONFIGURATION_TYPES)
	set(CMAKE_CONFIGURATION_TYPES Debug Release)
	set(CMAKE_CONFIGURATION_TYPES "${CMAKE_CONFIGURATION_TYPES}" CACHE STRING "Reset the configurations to what we need" FORCE)
endif()

# add a target to generate API documentation with Doxygen
# find_package(Doxygen REQUIRED)
# configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.ini ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile @ONLY)
configure_file("${PROJECT_SOURCE_DIR}/simo3d-config-in.h" "${PROJECT_BINARY_DIR}/simo3d-config.h")

# wxWidgets_USE_DEBUG
# wxWidgets_USE_UNICODE
# wxWidgets_USE_UNIVERSAL
# SET(wxWidgets_USE_STATIC, ON)

include_directories(SYSTEM "external/CML-1_0_4" )

include_directories(${CMAKE_SOURCE_DIR})


message(STATUS "Including Assimp ${ASSIMP_INCLUDE_DIR}")
include_directories(SYSTEM ${ASSIMP_INCLUDE_DIR})
include_directories(SYSTEM ${CMAKE_CURRENT_BINARY_DIR})

add_subdirectory(src)

