# FlyingCar
A portable sensor pack with laser range scanner, IMU, CSI camera, RGBD camera and SBC, no encoder needed.  
ydlidar laser: typeC to USB3.0  
realsense d435: typeC to USB3.0  
Sparkfun imu m0: miniUSB to USB3.0  
ydlidar power: miniUSB to USB3.0   

## Dependences  
**flyingcar_gazebo** imu simulation pkg
```bash
sudo apt install ros-kinetic-hector-gazebo-plugins  
```
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
git checkout 2.0.3
cd ~/workspace/catkin_ws
catkin_make
```
**flyingcar_sensors Lidar**
```bash
# build laser ros driver
cd ~/workspace/catkin_ws/src
git clone https://github.com/EAIBOT/ydlidar.git
cd ~/workspace/catkin_ws
catkin_make

# get the right device name
sudo ./initenv.sh
```
**flyingcar_sensors RGB**, because our CSI camera driver depend on [jetson-utils](https://github.com/dusty-nv/jetson-utils), so we need to build [jetson-inference](https://github.com/dusty-nv/jetson-inference). Moreover, we also tried [gscam](http://wiki.ros.org/gscam) and [jetson_csi_camera](https://github.com/peter-moran/jetson_csi_cam), but they do not work with jeston nano.
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
```
## Usage  
flyingcar_description
```bash
# robot model
roslaunch flyingcar_description spawn.launch
```
flyingcar_gazebo
```bash
# simulation
roslaunch flyingcar_gazebo spawn.launch
```
flyingcar_sensors
```bash
# imu 
roslaunch flyingcar_sensors imu.launch rviz:=1
# laser range finder
roslaunch flyingcar_sensors lidar.launch rviz:=1
# rgbd camera
roslaunch flyingcar_sensors rgbd.launch rviz:=1
# rgb camera
roslaunch flyingcar_sensors rgb.launch rviz:=1
```
test CSI camera with gstream, more test could be found at [JetsonHacksNano/CSI-Camera](https://github.com/JetsonHacksNano/CSI-Camera)
```bash
gst-launch-1.0 nvarguscamerasrc ! 'video/x-raw(memory:NVMM),width=3820, height=2464, framerate=21/1, format=NV12' ! nvvidconv flip-method=0 ! 'video/x-raw,width=960, height=616' ! nvvidconv ! nvegltransform ! nveglglessink -e
```
