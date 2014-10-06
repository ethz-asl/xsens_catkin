###########
## Setup ##
###########
cmake_minimum_required(VERSION 2.8.3)
project(xsens_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple(ALL_DEPS_REQUIRED)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/xsens)
ExternalProject_Add(${PROJECT_NAME}_src
  URL http://www.xsens.com/download/MT/4.2.1/mfmsdk_mtm_mtsdk_linux_4.2.1_beta.zip
  PATCH_COMMAND ""
  UPDATE_COMMAND ""
  BUILD_IN_SOURCE 1
  CONFIGURE_COMMAND cd ../${PROJECT_NAME}_src && tar -xzvf mtsdk_linux_69.tar.gz
  BUILD_COMMAND cd ../${PROJECT_NAME}_src/mtsdk/shared_object && make
  INSTALL_COMMAND mkdir -p ${CATKIN_DEVEL_PREFIX}/include/xsens
    && cp -r ../${PROJECT_NAME}_src/mtsdk/shared_object/include/xsens ${CATKIN_DEVEL_PREFIX}/include/xsens
    && cp ../${PROJECT_NAME}_src/mtsdk/shared_object/include/xsensdeviceapi.h ${CATKIN_DEVEL_PREFIX}/include/xsens
    && cp ../${PROJECT_NAME}_src/mtsdk/shared_object/include/xstypes.h ${CATKIN_DEVEL_PREFIX}/include/xsens
    && cp -r ../${PROJECT_NAME}_src/mtsdk/shared_object/lib/ ${CATKIN_DEVEL_PREFIX}
)

##################
## Installation ##
##################
cs_install()

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/xsens/
  DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN "*.hpp"
)

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  FILES_MATCHING 
    PATTERN "${CMAKE_SHARED_LIBRARY_PREFIX}xsens*${CMAKE_SHARED_LIBRARY_SUFFIX}*"
    PATTERN "${CMAKE_SHARED_LIBRARY_PREFIX}xstypes*${CMAKE_SHARED_LIBRARY_SUFFIX}*"
)

#############
## Exports ##
#############
cs_export(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/xsens
  LIBRARIES ${CMAKE_SHARED_LIBRARY_PREFIX}xstypes${CMAKE_SHARED_LIBRARY_SUFFIX} ${CMAKE_SHARED_LIBRARY_PREFIX}xsensdeviceapi${CMAKE_SHARED_LIBRARY_SUFFIX}
)
