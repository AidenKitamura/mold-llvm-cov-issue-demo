cmake_minimum_required(VERSION 3.24)

project(mold-issue-demo)

option(USE_MOLD "whether to use mold to link" OFF)

# global setups
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_SKIP_BUILD_RPATH TRUE)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_INSTALL_PREFIX ${CMAKE_BINARY_DIR})

add_executable(main_exe "${CMAKE_CURRENT_SOURCE_DIR}/main.cc")

add_library(dummy_lib STATIC "${CMAKE_CURRENT_SOURCE_DIR}/src/dummy.cc")
target_include_directories(dummy_lib PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include")

target_compile_options(main_exe PRIVATE -fprofile-instr-generate -fcoverage-mapping -O0)
target_compile_options(dummy_lib PRIVATE -fprofile-instr-generate -fcoverage-mapping -O0)
target_link_options(main_exe PRIVATE -fprofile-instr-generate -fcoverage-mapping)
target_link_options(dummy_lib PRIVATE -fprofile-instr-generate -fcoverage-mapping)

if(USE_MOLD)
    target_link_options(main_exe PRIVATE "-fuse-ld=mold" "-v")
    target_link_options(dummy_lib PRIVATE "-fuse-ld=mold" "-v")
endif()

target_link_libraries(main_exe PRIVATE dummy_lib)

install(TARGETS main_exe RUNTIME DESTINATION bin)
