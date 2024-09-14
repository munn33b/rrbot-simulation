cmake_minimum_required(VERSION 3.16)
project(rrbot_controllers LANGUAGES CXX)

if(CMAKE_CXX_COMPILER_ID MATCHES "(GNU|Clang)")
  add_compile_options(-Wall -Wextra)
endif()

# set the same behavior for windows as it is on linux
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

# find dependencies
set(THIS_PACKAGE_INCLUDE_DEPENDS
  hardware_interface
  pluginlib
  rclcpp
  rclcpp_lifecycle
)

# find dependencies
find_package(backward_ros REQUIRED)
find_package(ament_cmake REQUIRED)
foreach(Dependency IN ITEMS ${THIS_PACKAGE_INCLUDE_DEPENDS})
  find_package(${Dependency} REQUIRED)
endforeach()

## COMPILE
add_library(
  rrbot_controllers
  SHARED
  hardware/rrbot.cpp
)
target_compile_features(rrbot_controllers PUBLIC cxx_std_17)
target_include_directories(rrbot_controllers PUBLIC
$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/hardware/include>
$<INSTALL_INTERFACE:include/rrbot_controllers>
)
ament_target_dependencies(
  rrbot_controllers PUBLIC
  ${THIS_PACKAGE_INCLUDE_DEPENDS}
)

# Export hardware plugins
pluginlib_export_plugin_description_file(hardware_interface rrbot_controllers.xml)

# INSTALL
install(
  DIRECTORY hardware/include/
  DESTINATION include/rrbot_controllers
)
install(
  DIRECTORY urdf
  DESTINATION share/rrbot_controllers
)
install(
  DIRECTORY launch config
  DESTINATION share/rrbot_controllers
)
install(TARGETS rrbot_controllers
  EXPORT export_rrbot_controllers
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

if(BUILD_TESTING)
  find_package(ament_cmake_pytest REQUIRED)

  ament_add_pytest_test(rrbot_controllers_urdf_xacro test/test_urdf_xacro.py)
  ament_add_pytest_test(view_rrbot_controllers_launch test/test_view_robot_launch.py)
  ament_add_pytest_test(run_rrbot_controllers_launch test/test_rrbot_launch.py)
endif()


## EXPORTS
ament_export_targets(export_rrbot_controllers HAS_LIBRARY_TARGET)
ament_export_dependencies(${THIS_PACKAGE_INCLUDE_DEPENDS})
ament_package()
