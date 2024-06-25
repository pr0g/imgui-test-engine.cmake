cmake_minimum_required(VERSION 3.15)

project(imgui-test-engine.cmake LANGUAGES CXX)

include(FetchContent)
include(CMakePackageConfigHelpers)

FetchContent_Declare(
  imgui.cmake
  GIT_REPOSITORY https://github.com/pr0g/imgui.cmake.git
  GIT_TAG 886661da24e01ecfa601b5c3bd624ed2067c229f)
set(IMGUI_ENABLE_TEST_ENGINE
    ON
    CACHE INTERNAL "")
FetchContent_MakeAvailable(imgui.cmake)

add_library(${PROJECT_NAME} STATIC)
target_sources(
  ${PROJECT_NAME}
  PRIVATE imgui-test-engine/imgui_test_engine/imgui_capture_tool.cpp
          imgui-test-engine/imgui_test_engine/imgui_te_context.cpp
          imgui-test-engine/imgui_test_engine/imgui_te_coroutine.cpp
          imgui-test-engine/imgui_test_engine/imgui_te_engine.cpp
          imgui-test-engine/imgui_test_engine/imgui_te_exporters.cpp
          imgui-test-engine/imgui_test_engine/imgui_te_perftool.cpp
          imgui-test-engine/imgui_test_engine/imgui_te_ui.cpp
          imgui-test-engine/imgui_test_engine/imgui_te_utils.cpp)

include(GNUInstallDirs)

target_include_directories(
  ${PROJECT_NAME}
  PUBLIC $<BUILD_LOCAL_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/imgui-test-engine>
         $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)
target_compile_definitions(
  ${PROJECT_NAME}
  PUBLIC
    IMGUI_DISABLE_INCLUDE_IMCONFIG_H
    $<$<BOOL:${IMGUI_DISABLE_OBSOLETE_FUNCTIONS}>:IMGUI_DISABLE_OBSOLETE_FUNCTIONS>
)
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_11)
target_link_libraries(${PROJECT_NAME} PUBLIC imgui.cmake)

set_target_properties(${PROJECT_NAME} PROPERTIES DEBUG_POSTFIX "d")

install(
  TARGETS ${PROJECT_NAME}
  EXPORT ${PROJECT_NAME}-config
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})
install(
  EXPORT ${PROJECT_NAME}-config
  NAMESPACE ${PROJECT_NAME}::
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
  FILE ${PROJECT_NAME}-targets.cmake)
install(
  FILES imgui-test-engine/imgui_test_engine/imgui_capture_tool.h
        imgui-test-engine/imgui_test_engine/imgui_te_context.h
        imgui-test-engine/imgui_test_engine/imgui_te_coroutine.h
        imgui-test-engine/imgui_test_engine/imgui_te_engine.h
        imgui-test-engine/imgui_test_engine/imgui_te_exporters.h
        imgui-test-engine/imgui_test_engine/imgui_te_imconfig.h
        imgui-test-engine/imgui_test_engine/imgui_te_internal.h
        imgui-test-engine/imgui_test_engine/imgui_te_perftool.h
        imgui-test-engine/imgui_test_engine/imgui_te_ui.h
        imgui-test-engine/imgui_test_engine/imgui_te_utils.h
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

configure_package_config_file(
  ${PROJECT_NAME}-config.cmake.in ${PROJECT_NAME}-config.cmake
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake"
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})
