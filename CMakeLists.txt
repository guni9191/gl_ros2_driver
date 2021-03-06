cmake_minimum_required(VERSION 3.5)
project(gl_ros2_driver)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tf2_ros REQUIRED)

include_directories(
    include
)

add_library(serial
  include/serial/serial.cc
  include/serial/list_ports_linux.cc
  include/serial/unix.cc
  include/serial/win.cc
)
add_library(gl_driver
    include/gl_driver.cpp
)

add_executable(${PROJECT_NAME}_node ${CMAKE_CURRENT_SOURCE_DIR}/src/${PROJECT_NAME}_node.cpp)
target_link_libraries(${PROJECT_NAME}_node
    gl_driver
    serial
)
ament_target_dependencies(${PROJECT_NAME}_node 
    rclcpp
    std_msgs
    sensor_msgs
    geometry_msgs
    tf2_ros
)

install(
    TARGETS ${PROJECT_NAME}_node
    DESTINATION lib/${PROJECT_NAME}
)
install(
    DIRECTORY launch
    DESTINATION share/${PROJECT_NAME}
)
install(
    DIRECTORY rviz
    DESTINATION share/${PROJECT_NAME}
)

ament_package()
