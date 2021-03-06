cmake_minimum_required(VERSION 3.2)

### Basic config ##########################################################

set(APP_NAME c2ffi)
project(APP_NAME)

set(SOURCE_ROOT ${CMAKE_SOURCE_DIR})

### Modules ###############################################################

# set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "more...")
# include(stuff...)

find_package(LLVM 3.7 REQUIRED CONFIG)

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "LLVM installed in ${LLVM_INSTALL_PREFIX}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")
add_definitions(${LLVM_DEFINITIONS})
llvm_map_components_to_libnames(llvm_libs core support mcparser option bitreader)
message(STATUS "Using llvm libs: ${llvm_libs}")
link_directories(${LLVM_INSTALL_PREFIX}/lib)

#include(CMakeForceCompiler)
#CMAKE_FORCE_C_COMPILER("${LLVM_TOOLS_BINARY_DIR}/clang" clang)
#CMAKE_FORCE_CXX_COMPILER("${LLVM_TOOLS_BINARY_DIR}/clang++" clang++)

### Variables #############################################################

# set(var_name val CACHE type "Description")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -fno-exceptions -fno-rtti")

message(STATUS "CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")
message(STATUS "CMAKE_EXE_LINKER_FLAGS: ${CMAKE_EXE_LINKER_FLAGS}")

### Subdirs ###############################################################

# add_subdirectory(path)


### Source ################################################################

include_directories(
  ${LLVM_INCLUDE_DIRS}
  ${SOURCE_ROOT}/src/include
  )

file(GLOB SOURCE_GLOB
  ${SOURCE_ROOT}/src/*.cpp
  ${SOURCE_ROOT}/src/drivers/*.cpp
  )

file(GLOB HEADER_GLOB
  ${SOURCE_ROOT}/include/*.h
  ${SOURCE_ROOT}/include/c2ffi/*.h
  )

set(SOURCE_FILES
  ${SOURCE_GLOB}
  )

set(HEADER_FILES
  ${HEADER_GLOB}
  )

add_executable(${APP_NAME} ${SOURCE_FILES} ${HEADER_FILES})
target_link_libraries(${APP_NAME}
  clangFrontendTool
  clangFrontend
  clangDriver
  clangSerialization
  clangCodeGen
  clangParse
  clangSema
  clangStaticAnalyzerFrontend
  clangStaticAnalyzerCheckers
  clangStaticAnalyzerCore
  clangAnalysis
  clangARCMigrate
  clangRewriteFrontend
  clangRewrite
  clangEdit
  clangDynamicASTMatchers
  clangASTMatchers
  clangAST
  clangLex
  clangBasic
  clangFormat
  clangIndex
  #clang
  clangToolingCore
  clangTooling
  ${llvm_libs})

set(APP_BIN_DIR "${CMAKE_BINARY_DIR}/bin")
set_target_properties(${APP_NAME} PROPERTIES
  RUNTIME_OUTPUT_DIRECTORY "${APP_BIN_DIR}"
  )

install(TARGETS ${APP_NAME} DESTINATION bin)

### Custom Targets ########################################################

# add_custom_target(name
#   COMMAND ...
#   )
