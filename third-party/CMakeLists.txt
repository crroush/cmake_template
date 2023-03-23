find_package(PkgConfig REQUIRED)

# Add the install path to the search path for pkgconfig/cmake files
list(APPEND CMAKE_PREFIX_PATH ${CMAKE_CURRENT_BINARY_DIR}/opt/usr)

# Hack to download, unpack, and build non-cmake-based dependencies at configure
# time instead of build time.
configure_file(CMakeLists.txt.in third-party-download/CMakeLists.txt @ONLY)
execute_process(COMMAND ${CMAKE_COMMAND} -DDEPS_DIR=${CMAKE_CURRENT_SOURCE_DIR}
  -G "Unix Makefiles" .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/third-party-download )
if(result)
  message(FATAL_ERROR "CMake step for third-party failed: ${result}")
endif()
execute_process(COMMAND ${CMAKE_COMMAND} --build .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/third-party-download )
if(result)
  message(FATAL_ERROR "Build step for third-party failed: ${result}")
endif()

# fftw3, single precision float
pkg_search_module(fftw3
  REQUIRED fftw3f
  IMPORTED_TARGET GLOBAL
)

# Point to the static version of fftw3
target_link_libraries(PkgConfig::fftw3 INTERFACE ${fftw3_STATIC_LDFLAGS})

CPMAddPackage(
  NAME googlebench
  URL ${CMAKE_CURRENT_SOURCE_DIR}/benchmark-1.7.1.tar.gz
  VERSION v1.7.1
  OPTIONS "BENCHMARK_ENABLE_TESTING OFF"
)

CPMAddPackage(
  NAME googletest
  URL ${CMAKE_CURRENT_SOURCE_DIR}/googletest-1.13.0.tar.gz
  VERSION 1.12.1
  OPTIONS "INSTALL_GTEST_OFF"
)
