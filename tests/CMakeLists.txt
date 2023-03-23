add_executable(cmake_test cmake_test.cc)
target_link_libraries(cmake_test
  gtest
  gtest_main
  libcmake
  PkgConfig::fftw3)

add_test(cmake_test cmake_test)
