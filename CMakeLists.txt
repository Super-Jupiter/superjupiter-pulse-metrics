cmake_minimum_required(VERSION 3.16)
project(SuperJupiterPulseMetrics VERSION 0.1.1 LANGUAGES CXX)

set(_superjupiter_pulse_metrics_top_level OFF)
if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
  set(_superjupiter_pulse_metrics_top_level ON)
endif()
option(SUPERJUPITER_PULSE_METRICS_BUILD_TESTS "Build PulseMetrics tests" ${_superjupiter_pulse_metrics_top_level})
option(SUPERJUPITER_PULSE_METRICS_BUILD_EXAMPLES "Build PulseMetrics host example" ${_superjupiter_pulse_metrics_top_level})
option(SUPERJUPITER_PULSE_METRICS_INSTALL "Generate PulseMetrics install rules" ${_superjupiter_pulse_metrics_top_level})

add_library(SuperJupiterPulseMetrics INTERFACE)
add_library(SuperJupiter::PulseMetrics ALIAS SuperJupiterPulseMetrics)
set_target_properties(SuperJupiterPulseMetrics PROPERTIES EXPORT_NAME PulseMetrics)
target_include_directories(SuperJupiterPulseMetrics INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
  $<INSTALL_INTERFACE:include>)
target_compile_features(SuperJupiterPulseMetrics INTERFACE cxx_std_11)

if(SUPERJUPITER_PULSE_METRICS_BUILD_TESTS)
  enable_testing()
  add_executable(superjupiter-pulse-metrics-review tests/review_regressions.cpp)
  target_link_libraries(superjupiter-pulse-metrics-review PRIVATE SuperJupiter::PulseMetrics)
  add_test(NAME superjupiter-pulse-metrics-review COMMAND superjupiter-pulse-metrics-review)
  add_executable(superjupiter-pulse-metrics-tests tests/test.cpp)
  target_link_libraries(superjupiter-pulse-metrics-tests PRIVATE SuperJupiter::PulseMetrics)
  add_test(NAME superjupiter-pulse-metrics-tests COMMAND superjupiter-pulse-metrics-tests)
  add_executable(superjupiter-pulse-metrics-namespace-test tests/namespace_isolation.cpp)
  target_link_libraries(superjupiter-pulse-metrics-namespace-test PRIVATE SuperJupiter::PulseMetrics)
  add_test(NAME superjupiter-pulse-metrics-namespace-test COMMAND superjupiter-pulse-metrics-namespace-test)
endif()
if(SUPERJUPITER_PULSE_METRICS_BUILD_EXAMPLES)
  add_executable(superjupiter-pulse-metrics-replay tools/replay.cpp)
  target_link_libraries(superjupiter-pulse-metrics-replay PRIVATE SuperJupiter::PulseMetrics)
endif()
if(SUPERJUPITER_PULSE_METRICS_INSTALL)
  include(CMakePackageConfigHelpers)
  set(_superjupiter_pulse_metrics_package_dir lib/cmake/SuperJupiterPulseMetrics)
  install(TARGETS SuperJupiterPulseMetrics EXPORT SuperJupiterPulseMetricsTargets)
  install(FILES src/SuperJupiterPulseMetrics.h DESTINATION include)
  install(EXPORT SuperJupiterPulseMetricsTargets NAMESPACE SuperJupiter::
    DESTINATION ${_superjupiter_pulse_metrics_package_dir})
  configure_package_config_file(cmake/SuperJupiterPulseMetricsConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterPulseMetricsConfig.cmake
    INSTALL_DESTINATION ${_superjupiter_pulse_metrics_package_dir})
  write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterPulseMetricsConfigVersion.cmake
    VERSION ${PROJECT_VERSION} COMPATIBILITY SameMinorVersion ARCH_INDEPENDENT)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterPulseMetricsConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterPulseMetricsConfigVersion.cmake
    DESTINATION ${_superjupiter_pulse_metrics_package_dir})
  install(FILES LICENSE DESTINATION share/licenses/SuperJupiterPulseMetrics)
endif()
