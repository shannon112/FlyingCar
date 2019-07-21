# Husky Cartographer Demo

This tutorial shows you how to use [move_base](http://wiki.ros.org/move_base) with [Google Cartographer](https://github.com/googlecartographer) to perform autonomous planning and movement with simultaneous localization and mapping (SLAM), on a simulated Husky, or a factory-standard Husky with a laser scanner publishing on the */scan* topic.

Developed by Google, Cartographer is an open-source library used for real-time simultaneous localization and mapping (SLAM). Using LIDAR data, it generates submaps which are later optimized and scan-matched to provide real-time loop closure. As a result, Cartographer is able to successfully close loops and generate a consistent map each time whereas Gmapping can occasionally fail to do so, resulting in unique maps generated after every iteration [[1](https://lup.lub.lu.se/student-papers/search/publication/8915402)]. A variety of sensor configurations are supported allowing Cartographer to be used for a broad range of applications. In this demo, Cartographer uses IMU and LIDAR data from the Husky to perform real-time SLAM.

To adapt this demo to your own Husky, you may need to clone the [husky_cartographer_navigation](http://github.com/husky/husky_cartographer_navigation.git) repository, and modify the relevant parameters. To learn about move_base and the navigation stack, see the [Navigation Tutorials](http://wiki.ros.org/navigation/Tutorials). To learn more about Google Cartographer for ROS, see the [Cartographer ROS](https://google-cartographer-ros.readthedocs.io/en/latest/) documentation.

## Instructions

  1. To get started with 2-D SLAM using Google Cartographer, clone this repository into your working directory:

     `git clone http://github.com/husky/husky_cartographer_navigation.git`

  2. Run the following script to create a workspace and install proto3. This script will also install the packages required to use Cartographer as well as the [husky](https://github.com/husky/husky), [husky_desktop](https://github.com/husky/husky_desktop), and [husky_simulator](https://github.com/husky/husky_simulator) packages:

     `source $(pwd)/husky_cartographer_navigation/husky_cartographer_install.sh`

  3. Open two new terminal/tabs, source the workspace for each terminal/tab:

     `source install_isolated/setup.bash`

      1. Launch the Gazebo simulation:

         `roslaunch husky_gazebo husky_playpen.launch`

      2. Launch the Cartographer node to begin SLAM (NOTE: This also launches RViz to visualize the robot):

         `roslaunch husky_cartographer_navigation cartographer_demo.launch`

  4. In the Rviz visualizer, make sure the visualizers in the Navigation group are enabled.

  5. Use the 2D Nav Goal tool in the top toolbar to select a movement goal in the visualizer. Make sure to select an unoccupied (dark grey) or unexplored (light grey) location.

  6. As the robot moves, you should see the grey static map (map topic) grow. There might be discrete jumps in the map as the Cartographer algorithm attempts to localize the robot.

  ![Husky World Map](husky_cartographer.png)

  7. To save the generated map, you can run the map_saver utility:

     `rosrun map_server map_saver -f <filename>`  
     `rosrun map_server map_saver -f gridmap --occ 65 --free 20`  
     https://github.com/googlecartographer/cartographer_ros/issues/475

#### Tuning Cartographer

To tune Cartographer for low latency SLAM, edit the *husky.lua* configuration file found in the *husky_cartographer_navigation/config* directory.

For more information on tuning, click [here](http://google-cartographer-ros.readthedocs.io/en/latest/tuning.html)

[1] T. Coroiu and O. Hinton, "A Platform for Indoor Localisation,
Mapping, and Data Collection using an
Autonomous Vehicle," M.S. thesis, Lund Univ., Lund, Sweden, 2017.

```
sudo apt-get install ros-melodic-teleop-twist-keyboard
rosrun teleop_twist_keyboard teleop_twist_keyboard.py
```
```
通常使用命令 rospack profile 来使得这个新的目录能够在ROS中被查找到： 
rospack file
```
SLAM create 2d grid map
```
wget -P ~/Downloads https://storage.googleapis.com/cartographer-public-data/bags/backpack_2d/cartographer_paper_deutsches_museum.bag
roslaunch cartographer_ros demo_backpack_2d.launch bag_filename:=${HOME}/Downloads/cartographer_paper_deutsches_museum.bag
```
offline save .pbstream map and doing the localization task
```
wget -P ~/Downloads https://storage.googleapis.com/cartographer-public-data/bags/backpack_2d/b2-2016-04-05-14-44-52.bag
wget -P ~/Downloads https://storage.googleapis.com/cartographer-public-data/bags/backpack_2d/b2-2016-04-27-12-31-41.bag
roslaunch cartographer_ros offline_backpack_2d.launch bag_filenames:=${HOME}/Downloads/b2-2016-04-05-14-44-52.bag
roslaunch cartographer_ros demo_backpack_2d_localization.launch \
   load_state_filename:=${HOME}/Downloads/b2-2016-04-05-14-44-52.bag.pbstream \
   bag_filename:=${HOME}/Downloads/b2-2016-04-27-12-31-41.bag
```
grid+.pbstream map using same bag (two map could perfectly overlap)
```
mapping bag: b2-2016-04-05-14-44-52.bag
testing bag: b2-2016-04-27-12-31-41.bag

roslaunch cartographer_ros demo_backpack_2d.launch bag_filename:=${HOME}/Downloads/b2-2016-04-05-14-44-52.bag
rosrun map_server map_saver -f gridmapb2 --occ 65 --free 20
rosrun map_server map_server gridmapb2.yaml

roslaunch cartographer_ros offline_backpack_2d.launch bag_filenames:=${HOME}/Downloads/b2-2016-04-05-14-44-52.bag
roslaunch cartographer_ros demo_backpack_2d_localization.launch \
   load_state_filename:=/home/shannon/Documents/ros-kinetic/src/ironfish_cartographer_navigation/map/b2-2016-04-05-14-44-52.bag.pbstream \
   bag_filename:=${HOME}/Downloads/b2-2016-04-27-12-31-41.bag
```
save .pbstream state file
```
# Finish the first trajectory. No further data will be accepted on it.
rosservice call /finish_trajectory 0

# Ask Cartographer to serialize its current state.
rosservice call /write_state "{filename: '${HOME}/Downloads/map.bag.pbstream', include_unfinished_submaps: 1}"
```

pure localization bug -> checkout cartographer and cartographer_ros branch to master, then re-build
https://github.com/googlecartographer/cartographer/issues/1368   
https://github.com/googlecartographer/cartographer_ros/issues/1122   

because simulator would brocast odom indeed, so only seperate mode can be used  
if on real robot ros_control could turn off tf brocast case by case  
