cmake_minimum_required(VERSION 2.8.3)
project(flyingcar_cartographer)

find_package(catkin REQUIRED COMPONENTS roslaunch)

catkin_package()


install(DIRECTORY launch config
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(FILES cartographer.rviz
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
