cmake_minimum_required(VERSION 3.16)
project(rrbot_bringup LANGUAGES CXX)

if(CMAKE_CXX_COMPILER_ID MATCHES "(GNU|Clang)")
  add_compile_options(-Wall -Wextra)
endif()

# find dependencies
set(THIS_PACKAGE_INCLUDE_DEPENDS
)

# find dependencies
find_package(ament_cmake REQUIRED)
foreach(Dependency IN ITEMS ${THIS_PACKAGE_INCLUDE_DEPENDS})
  find_package(${Dependency} REQUIRED)
endforeach()

# INSTALL
install(
  DIRECTORY description/rviz
  DESTINATION share/ros2_control_demo_example_15
)
install(
  DIRECTORY launch config
  DESTINATION share/rrbot_bringup
)

if(BUILD_TESTING)
  find_package(ament_cmake_pytest REQUIRED)

  # TODO(christophfroehlich) deactivated because of bug in spawner
  # ament_add_pytest_test(test_rrbot_namespace_launch test/test_rrbot_namespace_launch.py)
  # ament_add_pytest_test(test_multi_controller_manager_launch test/test_multi_controller_manager_launch.py)

endif()

## EXPORTS
ament_export_dependencies(${THIS_PACKAGE_INCLUDE_DEPENDS})
ament_package()
