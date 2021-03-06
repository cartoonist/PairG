include(ExternalProject)

# Enabling `assert` by setting the build type to 'Debug' for test module
set(CMAKE_BUILD_TYPE "Debug")

## This block bundles VGio
#find_package(Protobuf REQUIRED)
#
## Find "HTSlib"
#find_package(HTSlib QUIET)
## Use bundled "HTSlib" if it is not installed
#if(NOT TARGET HTSlib::HTSlib)
#  set(HTSlib_SOURCE_DIR ${PROJECT_SOURCE_DIR}/ext/htslib)
#  ExternalProject_Add(htslib_git
#    DOWNLOAD_COMMAND git -C ${PROJECT_SOURCE_DIR} submodule update --init --recursive -- ${HTSlib_SOURCE_DIR}
#    SOURCE_DIR ${HTSlib_SOURCE_DIR}
#    BUILD_IN_SOURCE 1
#    CONFIGURE_COMMAND ""
#    BUILD_COMMAND make
#    INSTALL_COMMAND make install PREFIX=<INSTALL_DIR>)
#  ExternalProject_Get_Property(htslib_git INSTALL_DIR)
#  set(CMAKE_PREFIX_PATH "$<BUILD_INTERFACE:${INSTALL_DIR}/lib/pkgconfig>;$<INSTALL_INTERFACE:$<INSTALL_PREFIX>/lib/pkgconfig>" ${CMAKE_PREFIX_PATH})
#  find_package(HTSlib REQUIRED)
#endif()
#
## Find "VGio"
#find_package(VGio QUIET)
## Use bundled "VGio" if it is not installed
#if(NOT TARGET VGio::VGio)
#  set(VGio_SOURCE_DIR ${PROJECT_SOURCE_DIR}/ext/libvgio)
#  execute_process(COMMAND git submodule update --init --recursive -- ${VGio_SOURCE_DIR}
#    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
#  add_subdirectory(${VGio_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}/ext/libvgio)
#endif()
#
## If external libraries are added, the target depends on them assuring build
## order in parallel builds.
#if(TARGET htslib_git)
#  add_dependencies(pairg htslib_git)
#endif()

find_package(VGio REQUIRED)

# Prepare "Catch" library
if(NOT TARGET Catch2::Catch2)
  set(CATCH_SOURCE_DIR ${PROJECT_SOURCE_DIR}/ext/catch)
  execute_process(COMMAND git submodule update --init -- ${CATCH_SOURCE_DIR}
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
  add_subdirectory(${CATCH_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}/ext/catch)
endif()

##### add definitions to find path to test data files
add_definitions(-DPROJECT_TEST_DATA_DIR=${PROJECT_SOURCE_DIR}/tests/data)
include_directories(${PROJECT_SOURCE_DIR}/src)  # for `parseCmdArgs.hpp`
include_directories(${PROJECT_SOURCE_DIR}/ext)  # for `PaSGAL`, `clipp`, or `prettyprint`

add_executable(test_graph_load test_main.cpp test_graphLoad.cpp)
target_link_libraries(test_graph_load
  PRIVATE pairg::libpairg
  PRIVATE VGio::VGio
  PRIVATE Catch2::Catch2)

add_executable(test_pairing test_main.cpp test_pairing.cpp)
target_link_libraries(test_pairing
  PRIVATE pairg::libpairg
  PRIVATE VGio::VGio
  PRIVATE Catch2::Catch2)

add_executable(test_bfs test_main.cpp test_bfs.cpp)
target_link_libraries(test_bfs
  PRIVATE pairg::libpairg
  PRIVATE VGio::VGio
  PRIVATE Catch2::Catch2)
