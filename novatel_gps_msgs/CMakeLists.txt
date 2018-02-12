cmake_minimum_required(VERSION 3.5)
project(novatel_gps_msgs)

# Add support for C++11
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 11)
endif()

#find_package(ament_cmake REQUIRED)
#find_package(std_msgs REQUIRED)
#find_package(rosidl_default_generators REQUIRED)
#find_package(builtin_interfaces REQUIRED)

find_package(ament_cmake REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(marti_common_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(builtin_interfaces REQUIRED)


rosidl_generate_interfaces(${PROJECT_NAME} 
  "srv/NovatelFRESET.srv"
  "msg/Gpgga.msg"
  "msg/Gpgsa.msg"
  "msg/Gpgsv.msg"
  "msg/Gprmc.msg"
  "msg/Inscov.msg"
  "msg/Inspva.msg"
  "msg/Insstdev.msg"
  "msg/NovatelCorrectedImuData.msg"
  "msg/NovatelExtendedSolutionStatus.msg"
  "msg/NovatelMessageHeader.msg"
  "msg/NovatelPosition.msg"
  "msg/NovatelReceiverStatus.msg"
  "msg/NovatelSignalMask.msg"
  "msg/NovatelVelocity.msg"
  "msg/RangeInformation.msg"
  "msg/Range.msg"
  "msg/Satellite.msg"
  "msg/GpsTime.msg"
  "msg/TrackstatChannel.msg"
  "msg/Trackstat.msg"
  DEPENDENCIES 
  builtin_interfaces
  std_msgs
)

set(INCLUDE_DIRS 
  ${ament_cmake_INCLUDE_DIRS})
include_directories(${INCLUDE_DIRS})

set(LIBRARY_DIRS 
  ${ament_cmake_LIBRARY_DIRS})

link_directories(${LIBRARY_DIRS})

set(LIBS  ${rosidl_default_generators_LIBRARIES}
  ${ament_cmake_LIBRARIES})

set(MSG_DEPS std_msgs)

ament_export_dependencies(${MSG_DEPS})
#ament_export_dependencies(rosidl_default_generators)
ament_export_dependencies(ament_cmake)
ament_export_include_directories(${INCLUDE_DIRS})

ament_package()
