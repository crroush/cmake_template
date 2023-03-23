add_library(libcmake STATIC cmake_proj.cc)

target_include_directories(libcmake PUBLIC ${PROJECT_SOURCE_DIR}/inc)
# Stop cmake from prepending lib to the name
set_target_properties(libcmake PROPERTIES PREFIX "")
target_link_libraries(libcmake)
