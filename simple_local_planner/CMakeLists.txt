cmake_minimum_required(VERSION 2.8.3)
project(simple_local_planner)

find_package(catkin REQUIRED
        COMPONENTS
            base_local_planner
            cmake_modules
            costmap_2d
            dynamic_reconfigure
            nav_core
            nav_msgs
            pluginlib
            pcl_conversions
            roscpp
            tf
        )

find_package(Eigen3 REQUIRED)
find_package(PCL REQUIRED)
remove_definitions(-DDISABLE_LIBUSB-1.0)
include_directories(
    include
    ${catkin_INCLUDE_DIRS}
    ${EIGEN3_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
    )
add_definitions(${EIGEN3_DEFINITIONS})

link_directories(${catkin_LIBRARY_DIRS})

# dynamic reconfigure
generate_dynamic_reconfigure_options(
    cfg/SimplePlanner.cfg
)

catkin_package(
    INCLUDE_DIRS include
    LIBRARIES simple_local_planner
    CATKIN_DEPENDS
        dynamic_reconfigure
        pluginlib
        roscpp
)

file(GLOB SRCS "src/*.cpp")
add_library(simple_local_planner ${SRCS})
target_link_libraries(simple_local_planner ${catkin_LIBRARIES})
add_dependencies(simple_local_planner simple_local_planner_gencfg)
add_dependencies(simple_local_planner nav_msgs_generate_messages_cpp)

install(TARGETS simple_local_planner
       ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       )

install(FILES blp_plugin.xml
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  PATTERN ".svn" EXCLUDE
)
