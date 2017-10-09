cmake_minimum_required(VERSION 2.8.3)
project(rsv_balance_gazebo)

find_package(catkin REQUIRED COMPONENTS 
  gazebo_plugins
  gazebo_ros
  geometry_msgs
  nav_msgs
  roslaunch
  roslint
  rsv_balance_gazebo_control
  rsv_balance_msgs
  sensor_msgs
  std_msgs
)

find_package(gazebo REQUIRED)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${GAZEBO_INCLUDE_DIRS}
)

catkin_package(
  CATKIN_DEPENDS
    geometry_msgs
    nav_msgs
    rsv_balance_msgs
    sensor_msgs
    std_msgs
)

add_library(gazebo_rsv_balance src/gazebo_rsv_balance.cpp)
target_link_libraries(gazebo_rsv_balance ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES} rsv_balance_gazebo_control)
add_dependencies(gazebo_rsv_balance rsv_balance_msgs rsv_balance_gazebo_control)

install(
  TARGETS gazebo_rsv_balance
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(
  DIRECTORY include/
  DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

install(
  DIRECTORY launch rviz urdf models
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

roslint_cpp()
roslaunch_add_file_check(launch)

# check c++11 / c++0x
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "-std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "-std=c++0x")
else()
    message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()
