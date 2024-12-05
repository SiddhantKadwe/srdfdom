cmake_minimum_required(VERSION 3.7.2)
project(srdfdom VERSION 1.0.0)

# Boost configuration
set(BOOST_ROOT "/home/rsc/rsc/projects/libraries/dfki/dependancies/boost_1_82_0")
set(BOOST_LIBRARYDIR "${BOOST_ROOT}/stage/lib")
set(BOOST_INCLUDEDIR "${BOOST_ROOT}")

# Dependencies
find_package(Boost REQUIRED)
find_package(console_bridge REQUIRED)
find_package(urdfdom REQUIRED)
find_package(urdfdom_headers REQUIRED)
find_package(TinyXML2 REQUIRED)
find_package(urdf QUIET)

# Define compile option if URDF is found
if(urdf_FOUND)
  add_definitions(-DHAS_URDF)
  include_directories(${urdf_INCLUDE_DIRS})
endif()

# Include directories
include_directories(
  include
  ${Boost_INCLUDE_DIR}
  ${TinyXML2_INCLUDE_DIRS}
  ${console_bridge_INCLUDE_DIRS}
  ${urdfdom_INCLUDE_DIRS}
  ${urdfdom_headers_INCLUDE_DIRS}
)

# Library target
add_library(${PROJECT_NAME}
  src/model.cpp
  src/srdf_writer.cpp
)

# Target properties
set_target_properties(${PROJECT_NAME} PROPERTIES
  VERSION ${PROJECT_VERSION}
  SOVERSION 1
)

# Link libraries
target_link_libraries(${PROJECT_NAME}
  ${TinyXML2_LIBRARIES}
  ${console_bridge_LIBRARIES}
  ${urdfdom_headers_LIBRARIES}
)

# Installation
install(TARGETS ${PROJECT_NAME}
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME}
  FILES_MATCHING PATTERN "*.h"
)

# Generate and install pkg-config file
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/srdfdom.pc.in
  ${CMAKE_CURRENT_BINARY_DIR}/srdfdom.pc
  @ONLY
)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/srdfdom.pc
  DESTINATION lib/pkgconfig
)
