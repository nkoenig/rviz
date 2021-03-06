cmake_minimum_required(VERSION 2.8.3)
project(rviz)

find_package(Boost REQUIRED
  COMPONENTS
  filesystem
  program_options
  signals
  system
  thread
)

find_package(Eigen REQUIRED)

find_package(PkgConfig REQUIRED)

pkg_check_modules(OGRE OGRE)
pkg_check_modules(OGRE-OVERLAY OGRE-Overlay)
## Find OGRE Plugin path (not necessarily platform-independent, I guess)
execute_process(COMMAND
pkg-config --variable=plugindir OGRE
OUTPUT_VARIABLE OGRE_PLUGIN_PATH
OUTPUT_STRIP_TRAILING_WHITESPACE
)
message(STATUS OGRE_PLUGIN_PATH=${OGRE_PLUGIN_PATH})

if(APPLE)
  FIND_LIBRARY(Cocoa_LIBRARIES Cocoa)
  set(rviz_ADDITIONAL_LIBRARIES ${Cocoa_LIBRARIES})
endif()

find_package(OpenGL REQUIRED)

find_package(PythonLibs REQUIRED)

find_package(Qt4 REQUIRED COMPONENTS QtCore QtGui QtOpenGL)

find_package(urdfdom REQUIRED)

find_package(catkin REQUIRED
  COMPONENTS
  angles
  geometry_msgs
  image_geometry
  image_transport
  interactive_markers
  laser_geometry
  map_msgs
  message_filters
  nav_msgs
  pluginlib
  python_qt_binding
  resource_retriever
  rosbag
  rosconsole
  roscpp
  roslib
  rospy
  sensor_msgs
  std_msgs
  std_srvs
  tf
  urdf
  visualization_msgs
)

catkin_python_setup()

catkin_package(
  INCLUDE_DIRS
    src
    ${EIGEN_INCLUDE_DIRS}
    ${OGRE_INCLUDE_DIRS}
    ${OPENGL_INCLUDE_DIR}
    ${urdfdom_INCLUDE_DIRS}
  LIBRARIES
    rviz
    default_plugin
    ${OGRE_LIBRARIES}
    ${OGRE-OVERLAY_LIBRARIES}
    ${rviz_ADDITIONAL_LIBRARIES}
    ${OPENGL_LIBRARIES}
    ${urdfdom_LIBRARIES}
  CATKIN_DEPENDS
    geometry_msgs
    image_geometry
    image_transport
    interactive_markers
    laser_geometry
    map_msgs
    message_filters
    nav_msgs
    pluginlib
    resource_retriever
    roscpp
    roslib
    sensor_msgs
    std_msgs
    tf
    urdf
    visualization_msgs
)

include_directories(SYSTEM
  ${EIGEN_INCLUDE_DIRS}
  ${OGRE_INCLUDE_DIRS}
  ${OPENGL_INCLUDE_DIR}
  ${PYTHON_INCLUDE_PATH}
  ${urdfdom_INCLUDE_DIRS}
)
include_directories(src ${catkin_INCLUDE_DIRS})

link_directories(${OGRE_LIBRARY_DIRS})

include(${QT_USE_FILE})
add_definitions(-DQT_NO_KEYWORDS)

#### If gtk ends up being the best way to get the correct window
#### position under X11, this is how to compile it in.
#
# find_package(GTK2)
# include_directories(${GTK2_INCLUDE_DIRS})
# include_directories(/usr/include/gdk-pixbuf-2.0)

add_subdirectory(src)

install(DIRECTORY ogre_media
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
install(DIRECTORY icons
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
install(DIRECTORY images
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
install(FILES default.rviz
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
install(FILES plugin_description.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
