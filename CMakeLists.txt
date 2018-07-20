cmake_minimum_required(VERSION 2.8.3)
project(rt_string_msgs)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  rtt_roscomm
  message_generation
  std_msgs
)

## Make sure we have gencpp >= 0.6.0 which does not generate C++ headers
## if the package already provides one (plugin support).
if(gencpp_VERSION AND gencpp_VERSION VERSION_LESS "0.6.0")
  message(FATAL_ERROR "${PROJECT_NAME} requires gencpp version 0.6.0 or higher.")
endif()

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## Generate messages in the 'msg' folder
add_message_files(
  FILES
  rt_string.msg
)

## Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  std_msgs
)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS std_msgs message_runtime
)

###########
## Build ##
###########

## Build ROS transport plugin library
if(TARGET ${PROJECT_NAME}_generate_messages_cpp)
  if(NOT DEFINED CMAKE_BUILD_TYPE OR CMAKE_BUILD_TYPE STREQUAL "Release")
    set(CMAKE_BUILD_TYPE MinSizeRel)
  endif()

  include_directories(
    include
    include/orocos
    ${catkin_INCLUDE_DIRS}
  )

  orocos_typekit(rtt-rt_string_msgs-ros-transport
    src/ros_rt_string_msgs_transport.cpp
  )
  target_link_libraries(rtt-rt_string_msgs-ros-transport ${catkin_LIBRARIES})
  add_dependencies(rtt-rt_string_msgs-ros-transport ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
endif()

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark cpp header files for installation
install(FILES
  include/${PROJECT_NAME}/rt_string.h
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
install(DIRECTORY include/${PROJECT_NAME}/boost
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

## Install headers
install(
  DIRECTORY include/
  DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
)

## Generate Orocos package
orocos_generate_package(
  DEPENDS_TARGETS rtt_roscomm
)
