# Dependences  
**flyingcar_sensors IMU**, more detail could be found at [razor_imu_9dof ros wiki](http://wiki.ros.org/razor_imu_9dof). Calibration result at [here](https://github.com/shannon112/imu_calibration)
```bash
# build imu ros driver
cd ~/workspace/catkin_ws/src
git clone https://github.com/KristofRobot/razor_imu_9dof
cd ~/workspace/catkin_ws
catkin_make

# get the authority
sudo usermod -a -G dialout flyingcar
sudo reboot

# some dependences if error occurs, but the gui may not support on melodic (Ubuntu18.04), not found apt python-visual pkg
pip install pyserial
pip install vpython 

sudo apt install ros-kinetic-rviz-imu-plugin 
```
**flyingcar_sensors RGBD**, because librealsense could not directly install at ARM-based SBC, we would follow jetsonhacks [article](https://www.jetsonhacks.com/2019/05/16/jetson-nano-realsense-depth-camera/) to build the libraries.
```bash
# install the swapfile:
cd ~/workspace
git clone https://github.com/jetsonhacksnano/installSwapfile
cd installSwapfile
./installSwapfile.sh
cd ..

# install librealsense
git clone https://github.com/jetsonhacksnano/installLibrealsense
cd installLibrealsense
./installLibrealsense.sh -c

# patches to the kernel models
./patchUbuntu.sh

# build realsense ros driver
cd ~/workspace/catkin_ws/src
git clone https://github.com/intel-ros/realsense.git
cd realsense
git checkout 2.0.3 #(or 2.1.3)
cd ~/workspace/catkin_ws
catkin_make

# rs_rgbd launch dependence
sudo apt install ros-kinetic-rgbd-launch
```
**flyingcar_sensors Lidar**
```bash
# build laser ros driver
cd ~/workspace/catkin_ws/src
git clone https://github.com/YDLIDAR/ydlidar_ros.git
cd ~/workspace/catkin_ws
catkin_make

# get the right device name
sudo ./initenv.sh
```

**(New)flyingcar_sensors RGB** depend on [gscam](http://wiki.ros.org/gscam) and refer to [jetson_csi_camera](https://github.com/peter-moran/jetson_csi_cam)
```bash
sudo apt-get install ros-melodic-gscam
```

**(Old)flyingcar_sensors RGB**, because our CSI camera driver depend on [jetson-utils](https://github.com/dusty-nv/jetson-utils), so we need to build [jetson-inference](https://github.com/dusty-nv/jetson-inference). 
```bash
# clone the repo and submodules
cd ~/workspace
git clone -b onnx https://github.com/dusty-nv/jetson-inference
cd jetson-inference
git submodule update --init

# build from source
mkdir build
cd build
cmake ../
make

# install libraries
sudo make install

# some dependences if error occurs
sudo apt-get install doxygen qt4-qmake qt4-default libgl1-mesa-dev libglu1-mesa-dev libglew-dev

# build ros rgb csi camera driver
cd ~/workspace/catkin_ws/src
git clone https://github.com/shannon112/jetson_nano_camera.git
cd ~/workspace/catkin_ws
catkin_make
```
> NOTE: or using another pack on [jetson_utils](https://github.com/nicolas-beaufort/jetson-utils) and [jetson_ros](https://github.com/nicolas-beaufort/jetbot_ros)

# Calibration
**flyingcar_sensors IMU**
https://github.com/shannon112/imu_calibration.git

**flyingcar_sensors RGB cam**
get camera_info.yml
```
rosrun camera_calibration cameracalibrator.py --size 7x6 --square 0.108 image:=/csi_camera/image_raw
vim camera_info.ini
rosrun camera_calibration_parsers convert camera_info.ini camera_info.yaml
```
calculate true framerate
```
rostopic hz /csi_camera/image_raw
```
