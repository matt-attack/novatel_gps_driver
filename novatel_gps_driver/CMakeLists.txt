cmake_minimum_required(VERSION 3.5)

project(novatel_gps_driver)

# Add support for C++11
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 11)
endif()

find_package(ament_cmake REQUIRED)
find_package(novatel_gps_msgs REQUIRED)
find_package(swri_string_util REQUIRED)
find_package(swri_roscpp REQUIRED)

set(INCLUDE_DIRS include ${ament_cmake_INCLUDE_DIRS})
include_directories(${INCLUDE_DIRS})

set(LIBRARY_DIRS ${ament_cmake_LIBRARY_DIRS} )

link_directories(${LIBRARY_DIRS})

set(LIBS ${ament_cmake_LIBRARIES})

set(DEPS diagnostic_msgs diagnostic_updater gps_common_msgs nav_msgs
  novatel_gps_msgs sensor_msgs std_msgs swri_math_util swri_roscpp
  swri_serial_util swri_string_util tf2)

set(BUILD_DEPENDS ${DEPS})

find_package(Boost REQUIRED COMPONENTS system)

# libpcap doesn't provide PkgConfig or CMake modules
find_library(libpcap_LIBRARIES pcap)
if("${libpcap_LIBRARIES}" STREQUAL "pcap-NOTFOUND")
  set(libpcap_FOUND FALSE)
else()
  set(libpcap_FOUND TRUE)
endif()

add_library(${PROJECT_NAME} SHARED
  src/novatel_gps.cpp
  src/novatel_message_extractor.cpp
  src/parsers/bestpos.cpp
  src/parsers/bestvel.cpp
  src/parsers/corrimudata.cpp
  src/parsers/gpgga.cpp
  src/parsers/gpgsa.cpp
  src/parsers/gpgsv.cpp
  src/parsers/gprmc.cpp
  src/parsers/header.cpp
  src/parsers/inscov.cpp
  src/parsers/inspva.cpp
  src/parsers/insstdev.cpp
  src/parsers/parsing_utils.cpp
  src/parsers/range.cpp
  src/parsers/time.cpp
  src/parsers/trackstat.cpp)
target_link_libraries(${PROJECT_NAME} ${LIBS} ${Boost_LIBRARIES}
  ${libpcap_LIBRARIES})
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-std=c++11")
ament_target_dependencies(${PROJECT_NAME} 
  novatel_gps_msgs
  swri_roscpp
)

### Nodelets ###
add_library(${PROJECT_NAME}_nodelets SHARED src/nodelets/novatel_gps_nodelet.cpp)
target_link_libraries(${PROJECT_NAME}_nodelets ${LIBS} ${PROJECT_NAME})
set_target_properties(${PROJECT_NAME}_nodelets
  PROPERTIES
  COMPILE_FLAGS "-std=c++11")

### Nodes ###
#swri_nodelet_add_node(novatel_gps_node ${PROJECT_NAME} NovatelGpsNodelet)
#target_link_libraries(novatel_gps_node ${LIBS} ${PROJECT_NAME}_nodelets)
#set_target_properties(novatel_gps_node PROPERTIES COMPILE_FLAGS "-std=c++11")

### Build unit tests
if(BUILD_TESTING)
  find_package(rostest REQUIRED)

  ament_add_gtest(parser_tests test/parser_tests.cpp)
  target_link_libraries(parser_tests ${PROJECT_NAME})
  set_target_properties(parser_tests PROPERTIES COMPILE_FLAGS "-std=c++11")

  add_rostest_gtest(novatel_gps_tests test/novatel_gps_tests.test
    test/novatel_gps_tests.cpp)
  target_link_libraries(novatel_gps_tests ${PROJECT_NAME})
  set_target_properties(novatel_gps_tests PROPERTIES COMPILE_FLAGS "-std=c++11")
endif()

### Install Libraries and Headers ###
install(TARGETS 
  #novatel_gps_node
  ${PROJECT_NAME}
  ${PROJECT_NAME}_nodelets
  ARCHIVE
  DESTINATION lib
  LIBRARY
  DESTINATION lib
  RUNTIME
  DESTINATION lib/${PROJECT_NAME})
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME})

### Install plugin XML ###
install(FILES nodelet_plugins.xml
  DESTINATION share/${PROJECT_NAME})

### Install Launch Files ###
install(DIRECTORY launch DESTINATION share/${PROJECT_NAME})

ament_export_dependencies(ament_cmake)
ament_export_dependencies(${BUILD_DEPENDS})
ament_export_include_directories(${INCLUDE_DIRS})
ament_export_libraries(${PROJECT_NAME} ${PROJECT_NAME}_nodelets ${LIBS})

ament_package()
