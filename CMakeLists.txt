PROJECT(miniz
  LANGUAGES C
  VERSION "2.0.8")
cmake_minimum_required(VERSION 2.8)
if(CMAKE_BUILD_TYPE STREQUAL "")
  # CMake defaults to leaving CMAKE_BUILD_TYPE empty. This screws up
  # differentiation between debug and release builds.
  set(CMAKE_BUILD_TYPE "Release" CACHE STRING
    "Choose the type of build, options are: None (CMAKE_CXX_FLAGS or \
CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif ()

option(BUILD_EXAMPLES "Build examples" OFF)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

set(miniz_SOURCE miniz.c miniz_zip.c miniz_tinfl.c miniz_tdef.c)

add_library(${PROJECT_NAME} ${miniz_SOURCE})
target_include_directories(${PROJECT_NAME} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  set(EXAMPLE1_SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.c")
  set(EXAMPLE2_SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.c")
  set(EXAMPLE3_SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/examples/example3.c")
  set(EXAMPLE4_SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/examples/example4.c")
  set(EXAMPLE5_SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/examples/example5.c")
  set(EXAMPLE6_SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/examples/example6.c")
  set(MINIZ_TESTER_SRC_LIST
      "${CMAKE_CURRENT_SOURCE_DIR}/tests/miniz_tester.cpp"
      "${CMAKE_CURRENT_SOURCE_DIR}/tests/timer.cpp")
  
  add_executable(example1 ${EXAMPLE1_SRC_LIST})
  target_link_libraries(example1 miniz)
  add_executable(example2 ${EXAMPLE2_SRC_LIST})
  target_link_libraries(example2 miniz)
  add_executable(example3 ${EXAMPLE3_SRC_LIST})
  target_link_libraries(example3 miniz)
  add_executable(example4 ${EXAMPLE4_SRC_LIST})
  target_link_libraries(example4 miniz)
  add_executable(example5 ${EXAMPLE5_SRC_LIST})
  target_link_libraries(example5 miniz)
  add_executable(example6 ${EXAMPLE6_SRC_LIST})
  target_link_libraries(example6 miniz)
  if(${UNIX})
      target_link_libraries(example6 m)
  endif()
  
  # add_executable(miniz_tester ${MINIZ_TESTER_SRC_LIST})
  # target_link_libraries(miniz_tester miniz)
endif(BUILD_EXAMPLES)

####
# Installation (https://github.com/forexample/package-example) {

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

# Configuration
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

# Include module with fuction 'write_basic_package_version_file'
include(CMakePackageConfigHelpers)

# Note: PROJECT_VERSION is used as a VERSION
write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

# Use variables:
#   * targets_export_name
#   * PROJECT_NAME
configure_package_config_file(
    "cmake/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)

# }

install(TARGETS ${PROJECT_NAME}
    EXPORT ${targets_export_name}
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    INCLUDES DESTINATION ${include_install_dir}
)
file(GLOB INSTALL_HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/*.h)
install(FILES ${INSTALL_HEADERS} DESTINATION ${include_install_dir}/${PROJECT_NAME})
