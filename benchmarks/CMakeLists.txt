add_executable(cmake_bench cmake_bench.cc)
target_link_libraries(cmake_bench
  benchmark::benchmark)
