cmake_minimum_required(VERSION 3.5)
project(unitree_legged_real)


find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)

find_package(ros2_unitree_legged_msgs REQUIRED)

EXECUTE_PROCESS( COMMAND uname -m COMMAND tr -d '\n' OUTPUT_VARIABLE ARCHITECTURE )
if( ${ARCHITECTURE} STREQUAL "aarch64" )
    # Jetson Setup
    set(LEGGED_SDK_DIR "/home/unitree/Unitree/sdk/unitree_legged_sdk")
    set( LIBRARY_NAME "libunitree_legged_sdk_arm64.so" )
elseif( ${ARCHITECTURE} STREQUAL "x86_64" )
    # Local Setup
    set(LEGGED_SDK_DIR "/home/kimsooyoung/Downloads/unitree_legged_sdk_v3.5.1/unitree_legged_sdk")
    set( LIBRARY_NAME "libunitree_legged_sdk_amd64.so" )
else()
    message(FATAL_ERROR "Invalid Architecture CMake Stops")
endif()
include_directories(${LEGGED_SDK_DIR}/include)
include_directories(include)
link_directories(${LEGGED_SDK_DIR}/lib)
string(CONCAT LEGGED_SDK_NAME ${LIBRARY_NAME})
set(EXTRA_LIBS -pthread ${LEGGED_SDK_NAME} lcm)

set(CMAKE_CXX_FLAGS "-O3")

#message("-- CMAKE_SYSTEM_PROCESSOR: ${CMAKE_SYSTEM_PROCESSOR}")
#if("${CMAKE_SYSTEM_PROCESSOR}" MATCHES "x86_64.*")
#  set(ARCH amd64)
#else()
#  set(ARCH arm64)
#endif()
#set(EXTRA_LIBS -pthread libunitree_legged_sdk_${ARCH}.so lcm)

#include_directories(
#    include
#    ${CMAKE_SOURCE_DIR}/unitree_legged_sdk-master/include
#)
#link_directories(${CMAKE_SOURCE_DIR}/unitree_legged_sdk-master/lib)


add_executable(ros2_udp src/ros2_udp.cpp)
target_link_libraries(ros2_udp ${EXTRA_LIBS})
ament_target_dependencies(ros2_udp rclcpp ros2_unitree_legged_msgs)

add_executable(ros2_walk_example src/ros2_walk_example.cpp)
target_link_libraries(ros2_walk_example ${EXTRA_LIBS})
ament_target_dependencies(ros2_walk_example rclcpp ros2_unitree_legged_msgs)

add_executable(ros2_position_example src/ros2_position_example.cpp)
target_link_libraries(ros2_position_example ${EXTRA_LIBS})
ament_target_dependencies(ros2_position_example rclcpp ros2_unitree_legged_msgs)

install(
  DIRECTORY include/
  DESTINATION include
)

install(TARGETS
    ros2_udp
    ros2_walk_example
    ros2_position_example
    DESTINATION lib/${PROJECT_NAME}
)

ament_package()




