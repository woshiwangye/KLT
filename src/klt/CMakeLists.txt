cmake_minimum_required(VERSION 3.16)
project(klt)

IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Release)
ENDIF()

MESSAGE("Build type: " ${CMAKE_BUILD_TYPE})
IF("${CMAKE_BUILD_TYPE}" STREQUAL "Release" OR "${CMAKE_BUILD_TYPE}" STREQUAL "RELEASE")
   set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O3 -march=native")
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -fopenmp")
ELSE()
   set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall")
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -fsanitize=leak -g")
ENDIF()

find_package(catkin REQUIRED COMPONENTS
  nav_msgs
  rosbag
  roscpp
  sensor_msgs
  std_msgs
  tf
  visualization_msgs
)

find_package(OpenCV 4.5 REQUIRED)
if(NOT OpenCV_FOUND)
  message(FATAL_ERROR "OpenCV > 4.5 not found.")
endif()

MESSAGE("opencv include dirs: " ${OpenCV_INCLUDE_DIRS})

catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS nav_msgs rosbag roscpp sensor_msgs std_msgs tf visualization_msgs
)

include_directories(
    ${OpenCV_INCLUDE_DIRS}
    # ${OpenGL_INCLUDE_DIR}
)

add_executable(imshow apps/rs-imshow.cpp
)

link_directories(/usr/lib/x86_64-linux-gnu)
target_link_libraries(imshow
    ${OpenCV_LIBS}
)

install(TARGETS imshow
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
)
