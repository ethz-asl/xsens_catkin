###########
## Setup ##
###########
cmake_minimum_required(VERSION 2.8.3)
project(xsens_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple(ALL_DEPS_REQUIRED)

include(ExternalProject)


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

file(GLOB_RECURSE XSENS_LIBS_ ${CMAKE_SHARED_LIBRARY_PREFIX}xsens*${CMAKE_SHARED_LIBRARY_SUFFIX}*)
message(WARNING "XSENS_LIBS__ ${XSENS_LIBS_}")
message(WARNING "CMAKE_SHARED_LIBRARY_PREFIX ${CMAKE_SHARED_LIBRARY_PREFIX}")
message(WARNING "CMAKE_SHARED_LIBRARY_SUFFIX ${CMAKE_SHARED_LIBRARY_SUFFIX}")
set(XSENS_LIBS ${XSENS_LIBS_})
file(GLOB_RECURSE XSENS_LIBS_ ${CMAKE_SHARED_LIBRARY_PREFIX}xstypes*${CMAKE_SHARED_LIBRARY_SUFFIX}*)
set(XSENS_LIBS ${XSENS_LIBS} ${XSENS_LIBS_})

##################
## Installation ##
##################
cs_install()

install(
  DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_src-prefix/src/${PROJECT_NAME}_src/mtsdk/shared_object/include/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}/../xsens
  FILES_MATCHING PATTERN "*.h"
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
#message(STATUS "+++ CMAKE_INSTALL_PREFIX = ${CMAKE_INSTALL_PREFIX}")
#message(STATUS "+++ CATKIN_DEVEL_PREFIX = ${CATKIN_DEVEL_PREFIX}")
file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/xsens)
file(MAKE_DIRECTORY ${CATKIN_PACKAGE_INCLUDE_DESTINATION}/../xsens)

#set(${CATKIN_DEVEL_PREFIX}/include/xsens PROPERTIES GENERATED TRUE)
#set(${CATKIN_PACKAGE_INCLUDE_DESTINATION}/../xsens PROPERTIES GENERATED TRUE)
cs_export(
  INCLUDE_DIRS ${CATKIN_PACKAGE_INCLUDE_DESTINATION}/../xsens ${CATKIN_DEVEL_PREFIX}/include/xsens
  LIBRARIES ${CMAKE_SHARED_LIBRARY_PREFIX}xstypes${CMAKE_SHARED_LIBRARY_SUFFIX} ${CMAKE_SHARED_LIBRARY_PREFIX}xsensdeviceapi${CMAKE_SHARED_LIBRARY_SUFFIX}
  #CFG_EXTRAS ${PROJECT_NAME}-extras.cmake
)
