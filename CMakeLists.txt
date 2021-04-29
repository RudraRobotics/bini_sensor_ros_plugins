cmake_minimum_required(VERSION 3.0.2)
project(bini_sensor_ros_plugins)

add_definitions(-std=c++11)

# set(CMAKE_BUILD_TYPE RelWithDebInfo)

find_package(catkin REQUIRED COMPONENTS
  gazebo_dev
  sensor_msgs
  image_transport
  geometry_msgs
  std_msgs
  roscpp
  tf
  gazebo_plugins
  message_filters
  tf2_ros
  tf2
  laser_geometry
  bini_sensor_ros_plugins_msgs
)

find_package(Eigen3 REQUIRED)
find_package(OGRE REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Protobuf REQUIRED)
find_package(Boost REQUIRED COMPONENTS system)


# Add Gazebo custom protobuf messages
set(PROTOBUF_IMPORT_DIRS "")
foreach(ITR ${GAZEBO_INCLUDE_DIRS})
  if(ITR MATCHES ".*gazebo-[0-9.]+$")
    set(PROTOBUF_IMPORT_DIRS "${ITR}/gazebo/msgs/proto")
  endif()
endforeach()

message(STATUS "PROTOBUF_IMPORT_DIRS = " ${PROTOBUF_IMPORT_DIRS})

set(GAZEBO_MSG_INCLUDE_DIRS)
foreach(ITR ${GAZEBO_INCLUDE_DIRS})
  if(ITR MATCHES ".*gazebo-[0-9.]+$")
    set(GAZEBO_MSG_INCLUDE_DIRS "${ITR}/gazebo/msgs")
  endif()
endforeach()

message(STATUS "GAZEBO_MSG_INCLUDE_DIRS = " ${GAZEBO_MSG_INCLUDE_DIRS})

file(GLOB msgs msgs/*.proto)

catkin_package()

###########
## Build ##
###########

include_directories(
  ${PROJECT_SOURCE_DIR}/include
  ${CMAKE_CURRENT_BINARY_DIR} # for generated messages
  ${catkin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  ${GAZEBO_INCLUDE_DIRS}
  ${GAZEBO_MSG_INCLUDE_DIRS}
  ${CMAKE_CURRENT_BINARY_DIR}
  ${OGRE_INCLUDE_DIRS}
  ${OGRE_Paging_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIR}
)

link_directories(
  ${GAZEBO_LIBRARY_DIRS}
)

# Add Gazebo custom protobuf messages
set(PROTOBUF_IMPORT_DIRS "")
foreach(ITR ${GAZEBO_INCLUDE_DIRS})
  if(ITR MATCHES ".*gazebo-[0-11.]+$")
    set(PROTOBUF_IMPORT_DIRS "${ITR}/gazebo/msgs/proto")
  endif()
endforeach()

message(STATUS "PROTOBUF_IMPORT_DIRS = " ${PROTOBUF_IMPORT_DIRS})

set(GAZEBO_MSG_INCLUDE_DIRS)
foreach(ITR ${GAZEBO_INCLUDE_DIRS})
  if(ITR MATCHES ".*gazebo-[0-11.]+$")
    set(GAZEBO_MSG_INCLUDE_DIRS "${ITR}/gazebo/msgs")
  endif()
endforeach()

message(STATUS "GAZEBO_MSG_INCLUDE_DIRS = " ${GAZEBO_MSG_INCLUDE_DIRS})

file(GLOB msgs msgs/*.proto)

protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS ${msgs})
add_library(bini_sensor_gazebo_msgs SHARED ${PROTO_SRCS})
target_link_libraries(bini_sensor_gazebo_msgs ${PROTOBUF_LIBRARY} ${GAZEBO_LIBRARIES})

add_library(bini_gazebo_ros_magnetometer_plugin
  src/MagnetometerROSPlugin.cc
  src/ROSBasePlugin.cc
  src/ROSBaseModelPlugin.cc)
target_link_libraries(bini_gazebo_ros_magnetometer_plugin ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
add_dependencies(bini_gazebo_ros_magnetometer_plugin bini_sensor_gazebo_msgs ${catkin_EXPORTED_TARGETS})
