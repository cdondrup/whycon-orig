cmake_minimum_required(VERSION 2.8.3)
project(whycon_orig)
 
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)
 
# Find ROS libraries needed for compilation
find_package(catkin REQUIRED COMPONENTS
    message_generation
    image_transport
    message_generation
    roscpp
    rospy
    sensor_msgs
    std_msgs
    visualization_msgs
  )
 
# Find SDL Library
find_package(SDL REQUIRED)
If (NOT SDL_FOUND)
  Message (FATAL_ERROR "Couldn't find SDL development files. On Debian-based systems (such as Ubuntu) you should install the 'libsdl2-ttf-dev' package.")
Else (NOT SDL_FOUND)
  INCLUDE_DIRECTORIES(${SDL_INCLUDE_DIR})
EndIf (NOT SDL_FOUND)
 
# Find SDL_ttf Library
Find_Package(SDL_ttf REQUIRED)
If (NOT SDLTTF_FOUND)
  Message (FATAL_ERROR "Couldn't find SDL-ttf development files. On Debian-based systems (such as Ubuntu) you should install the 'libsdl2-ttf-dev' package.")
Else (NOT SDLTTF_FOUND)
  INCLUDE_DIRECTORIES(${SDLTTF_INCLUDE_DIR})
EndIf (NOT SDLTTF_FOUND)
 
catkin_package(
  INCLUDE_DIRS
  src/camera
  src/gui
  src/common
  src/imageproc
)
 
# ###########
# ## Build ##
# ###########
 
include_directories(
  src/camera
  src/obj
  src/gui
  src/common
  src/imageproc
  ${catkin_INCLUDE_DIRS}
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
 
# Libraries
  add_library(CCamera src/camera/CCamera.cpp src/camera/avilib.c src/camera/v4l2uvc.c src/camera/color.c src/camera/utils.c)
  add_library(CGui src/gui/CGui.cpp)
  add_library(CMessage src/common/CMessage.cpp)
  add_library(CPosition src/common/CPosition.cpp)
  add_library(CThread src/common/CThread.cpp)
  add_library(CPoint src/common/CPoint.cpp)
  add_library(CTimer src/common/CTimer.cpp)
  add_library(cmath src/common/cmath.cpp)
  add_library(CPositionServer src/imageproc/CPositionServer.cpp)
  add_library(sysmat src/imageproc/sysmat.cpp)
 
  add_library(CRawImage src/common/CRawImage.cpp)
  add_library(CCircleDetect src/imageproc/CCircleDetect.cpp src/common/cmath.cpp)
  add_library(CNecklace src/imageproc/CNecklace.cpp)
  add_library(CTransformation src/imageproc/CTransformation.cpp src/imageproc/sysmat.cpp)
  add_library(FindCircle src/main/whycon.cpp)
 
 
add_dependencies(CTransformation ${PROJECT_NAME}_generate_messages_cpp ${catkin_EXPORTED_TARGETS})
add_dependencies(FindCircle ${PROJECT_NAME}_generate_messages_cpp ${catkin_EXPORTED_TARGETS})

message("SDL " ${SDL_LIBRARY})
message("SDL_ttf " ${SDLTTF_LIBRARY})
message("SDL " ${SDL_INCLUDE_DIR})

target_link_libraries(CTransformation
${SDL_LIBRARY}
${SDLTTF_LIBRARY}
${catkin_LIBRARIES}
)
 
 
# # Executables
add_executable(circle_finder src/main/whycon.cpp)
# add_dependencies(circle_finder ${PROJECT_NAME}_generate_messages_cpp ${catkin_EXPORTED_TARGETS})
 
target_link_libraries (circle_finder
    CCamera
    CGui
    CMessage
    CPosition
    CThread
    CPoint
    CTimer
    cmath
    CPositionServer
    sysmat
    CRawImage
    CCircleDetect
    CNecklace
    CTransformation
    ${SDL_LIBRARIES}
    ${SDL_ttf_LIBRARIES}
    ${catkin_LIBRARIES}
    )
 
# #############
# ## Install ##
# #############
 
# install(TARGETS
#     CCamera
#     CGui
#     CMessage
#     CPosition
#     CThread
#     CPoint
#     CTimer
#     cmath
#     CPositionServer
#     sysmat
#     CRawImage
#     CCircleDetect
#     CNecklace
#     CTransformation
#     ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#     LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#     RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
#     )
 
#  install(DIRECTORY include/
#     DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#     FILES_MATCHING PATTERN "*.h"
#     PATTERN ".git" EXCLUDE
#     )
 
# install(DIRECTORY launch
#     DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
#     )
 
# install(DIRECTORY doc
#     DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
#     )