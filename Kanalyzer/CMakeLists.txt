cmake_minimum_required(VERSION 3.13.4)
set(CMAKE_C_COMPILER "clang-10")
set(CMAKE_CXX_COMPILER "clang++-10")
set (CMAKE_EXE_LINKER_FLAGS -fopenmp)
project(Analyzer)

find_package(LLVM REQUIRED CONFIG)
find_package(Boost COMPONENTS system)
find_package(OpenMP REQUIRED)
if(Boost_FOUND)
include_directories(${Boost_INCLUDE_DIRS})
endif()

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

# Set your project compile flags.
# E.g. if using the C++ header files
# you will need to enable C++11 support
# for your compiler.
SET (CMAKE_CXX_FLAGS "-std=c++14")

include_directories(${LLVM_INCLUDE_DIRS})
separate_arguments(LLVM_DEFINITIONS_LIST NATIVE_COMMAND ${LLVM_DEFINITIONS})
add_definitions(${LLVM_DEFINITIONS_LIST})
include_directories("/usr/lib/llvm-10/lib/clang/10.0.1/include")
# Now build our tools
file(GLOB_RECURSE source_files "${CMAKE_CURRENT_SOURCE_DIR}/src/lib/*.cc" "${CMAKE_CURRENT_SOURCE_DIR}/src/lib/*.h")
list(REMOVE_ITEM source_files "${CMAKE_CURRENT_SOURCE_DIR}/src/lib/tmp.cc" "${CMAKE_CURRENT_SOURCE_DIR}/src/lib/utility.cc")
add_executable(Analyzer ${source_files})

# Find the libraries that correspond to the LLVM components
# that we wish to use
llvm_map_components_to_libnames(llvm_libs support core irreader analysis)

# Link against LLVM libraries
target_link_libraries(Analyzer ${llvm_libs} ${Boost_LIBRARIES} ${OpenMP_LIBRARIES})