cmake_minimum_required(VERSION 3.22 FATAL_ERROR)

# Prefer clang when present.
find_program(CMAKE_C_COMPILER   NAMES $ENV{CC}  clang   gcc PATHS ENV PATH NO_DEFAULT_PATH)
find_program(CMAKE_CXX_COMPILER NAMES $ENV{CXX} clang++ g++ PATHS ENV PATH NO_DEFAULT_PATH)

project(cmake_proj)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

option(BUILD_SHARED_LIBS "Build using shared libraries" OFF)

# Avoid warning about DOWNLOAD_EXTRACT_TIMESTAMP in CMake 3.24:
if (CMAKE_VERSION VERSION_GREATER_EQUAL "3.24.0")
	cmake_policy(SET CMP0135 NEW)
endif()

# cmake package manager
include(cmake/CPM.cmake)

# Default third-party installation prefix, user-settable
set(DEPS_INSTALL_PREFIX ${CMAKE_CURRENT_BINARY_DIR}/third-party/opt CACHE PATH
  "third-party intallation path")

add_subdirectory(third-party)

# Enable test discovery, this should come after third-party so we don't execute
# all of _their_ tests as well.
enable_testing()

# Default compiler flags for our code
set(PROJ_C_FLAGS  "-march=native -Wall -Wextra -Wpedantic")
set(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS}   ${PROJ_C_FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${PROJ_C_FLAGS}")

# -Werror on release builds
set(CMAKE_C_FLAGS_RELEASE   "${CMAKE_C_FLAGS_RELEASE}   -Werror")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -Werror")

add_subdirectory(lib)
add_subdirectory(src)
add_subdirectory(tests)
add_subdirectory(benchmarks)
