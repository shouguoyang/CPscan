cmake_minimum_required(VERSION 2.8)
set(CMAKE_C_COMPILER "clang-10")
set(CMAKE_CXX_COMPILER "clang++-10")
project(llvmtest)

set(LLVM_TARGETS_TO_BUILD X86)
set(LLVM_BUILD_TOOLS OFF)

find_package(LLVM REQUIRED CONFIG)

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

SET (CMAKE_CXX_COMPILER_ENV_VAR "clang++")

SET (CMAKE_CXX_FLAGS "-std=c++14")
SET (CMAKE_CXX_FLAGS_DEBUG   "-g")
SET (CMAKE_CXX_FLAGS_RELWITHDEBINFO "-O2 -g")

SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

file(GLOB_RECURSE source_files "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")
add_executable(llvmtest ${source_files})
install(TARGETS llvmtest RUNTIME DESTINATION bin)

# Find the libraries that correspond to the LLVM components
# that we wish to use
llvm_map_components_to_libnames(llvm_libs
    Core
    ExecutionEngine
    Interpreter
    MC
    Support
    nativecodegen)

# Link against LLVM libraries
target_link_libraries(llvmtest ${llvm_libs})