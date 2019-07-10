# FlyingCar
A portable sensor pack with laser range scanner, IMU, CSI camera, RGBD camera and SBC, no encoder needed.  
ydlidar laser: typeC to USB3.0  
realsense d435: typeC to USB3.0  
Sparkfun imu m0: miniUSB to USB3.0  
ydlidar power: miniUSB to USB3.0   

## Dependences  
flyingcar_gazebo imu simulation pkg
```
sudo apt install ros-kinetic-hector-gazebo-plugins  
```
flyingcar_imu
```
```
flyingcar_rgbd
```
```
flyingcar_laser
```
```
flyingcar_rgb, because our CSI camera driver depend on [jetson-utils](https://github.com/dusty-nv/jetson-utils), so we need to build [jetson-inference](https://github.com/dusty-nv/jetson-inference).
```
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
```
## Usage  
flyingcar_imu
```
```
flyingcar_rgbd
```
```
flyingcar_laser
```
roslaunch ydlidar lidar_view.launch  
```
flyingcar_rgb
```
```
