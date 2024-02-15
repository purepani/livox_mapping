cmake_minimum_required(VERSION 2.8.3)
project(livox_mapping)

SET(CMAKE_BUILD_TYPE "Debug")

#set(CMAKE_CXX_COMPILER "/usr/bin/clang++")

#set(CMAKE_C_COMPILER "/usr/bin/clang")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fexceptions")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -pthread -std=c++0x -std=c++14 -fexceptions -Wno-unused-local-typedefs")

find_package(OpenMP QUIET)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}   ${OpenMP_C_FLAGS}")

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  nav_msgs
  sensor_msgs
  roscpp
  rospy
  std_msgs
  pcl_ros
  tf
  livox_ros_driver
  )

find_package(Eigen3 REQUIRED)
find_package(PCL REQUIRED)
find_package(OpenCV REQUIRED)

include_directories(
	${catkin_INCLUDE_DIRS} 
        ${EIGEN3_INCLUDE_DIR}
	${PCL_INCLUDE_DIRS})

catkin_package(
  CATKIN_DEPENDS geometry_msgs nav_msgs roscpp rospy std_msgs
  LIBRARIES livox_mapping
  DEPENDS EIGEN3 PCL OpenCV
  INCLUDE_DIRS
)

add_executable(livox_repub livox_mapping/livox_repub.cpp)
add_dependencies(livox_repub ${catkin_EXPORTED_TARGETS})
target_link_libraries(livox_repub ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS})

add_executable(loam_scanRegistration livox_mapping/scanRegistration.cpp)
target_link_libraries(loam_scanRegistration ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS})

add_executable(loam_scanRegistration_horizon livox_mapping/scanRegistration_horizon.cpp)
target_link_libraries(loam_scanRegistration_horizon ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS})

add_executable(loam_laserMapping livox_mapping/laserMapping.cpp)
target_link_libraries(loam_laserMapping ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS})

install(TARGETS livox_repub loam_scanRegistration loam_scanRegistration_horizon loam_laserMapping 
	ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
	LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
	RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
	)

install(DIRECTORY
  launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
	)
