cmake_minimum_required(VERSION 2.8.3)
project(glog_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

set(VERSION 0.3.5)
ExternalProject_Add(glog_src
  URL https://github.com/google/glog/archive/v${VERSION}.zip
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../glog_src/ && ./configure --with-pic
    --with-gflags=${gflags_catkin_PREFIX}
    --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../glog_src/ && make -j 8
  INSTALL_COMMAND cd ../glog_src/ && make install -j 8
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} glog_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libglog${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS glog-extras.cmake)
