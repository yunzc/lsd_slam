cmake_minimum_required(VERSION 2.4.6)
project(lsd_slam_viewer)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

ADD_SUBDIRECTORY(${PROJECT_SOURCE_DIR}/thirdparty/Sophus)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake)

find_package(OpenGL REQUIRED)
set(QT_USE_QTOPENGL TRUE)
set(QT_USE_QTXML TRUE)
find_package(QGLViewer REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(OpenCV REQUIRED)

find_package(catkin REQUIRED COMPONENTS 
  roscpp 
  std_msgs 
  dynamic_reconfigure 
  message_generation
)

# Messages & Services
# Generate messages in the 'msg' folder
add_message_files(
  FILES
  keyframeGraphMsg.msg
  keyframeMsg.msg
)

generate_messages(
  DEPENDENCIES
  std_msgs
)

# Generate Dynamic Reconfigure
generate_dynamic_reconfigure_options(
  LSDSLAMViewerParams.cfg
)

catkin_package(
 CATKIN_DEPENDS message_runtime
)

include_directories(
  ${OpenCV_INCLUDE_DIRS}
  ${catkin_INCLUDE_DIRS} 
  ${QT_INCLUDES} 
  ${EIGEN3_INCLUDE_DIR} 
  ${QGLVIEWER_INCLUDE_DIR}
)

# SSE Sources files
set(SOURCE_FILES         
  src/PointCloudViewer.cpp
  src/KeyFrameDisplay.cpp
  src/KeyFrameGraphDisplay.cpp
  src/settings.cpp
)

set(HEADER_FILES     
  src/PointCloudViewer.h
  src/KeyFrameDisplay.h
  src/KeyFrameGraphDisplay.h
  src/settings.h
)

include_directories(
  ${PROJECT_SOURCE_DIR}/thirdparty/Sophus
)  

add_executable(viewer src/main_viewer.cpp ${SOURCE_FILES} ${HEADER_FILES})

add_dependencies(viewer ${catkin_EXPORTED_TARGETS})

target_link_libraries(viewer ${QGLViewer_LIBRARIES} ${QT_LIBRARIES} ${QGLVIEWER_LIBRARY} ${OpenCV_LIBRARIES} GL glut GLU)

include_directories(${Boost_INCLUDE_DIRS})
target_link_libraries(viewer ${Boost_LIBRARIES}) 

add_executable(videoStitch src/main_stitchVideos.cpp)

