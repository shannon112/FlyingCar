# FlyingCar
A portable sensor pack with laser range scanner, IMU, CSI camera, RGBD camera and SBC, no encoder needed.  

ydlidar laser: typeC to USB3.0  
realsense d435: typeC to USB3.0  
Sparkfun imu m0: miniUSB to USB3.0  
ydlidar power: miniUSB to USB2.0 (power only)   
jetson nano power: miniUSB to USB2.0 (power only)   

<img src="https://github.com/shannon112/FlyingCar/blob/master/flyingcar_bringup/result/device_back.jpg" height=270/> <img src="https://github.com/shannon112/FlyingCar/blob/master/flyingcar_bringup/result/device_front.jpg" height=270/> <img src="https://github.com/shannon112/FlyingCar/blob/master/flyingcar_bringup/result/real_rviz.png" height=270/>

## Usage  
flyingcar_description
```bash
# robot model
roslaunch flyingcar_description spawn.launch rviz:=1
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
flyingcar_bringup
```bash
# bringup real sensors(rgbd, laser, imu) on FlyingCar
roslaunch flyingcar_bringup spawn_livd.launch rviz:=0
# bringup real sensors(rgb, laser, imu) on FlyingCar
roslaunch flyingcar_bringup spawn_liv.launch rviz:=0
# bringup real sensors(laser, imu) on FlyingCar
roslaunch flyingcar_bringup spawn_li.launch rviz:=0
# monitor sensor data by pc
roslaunch flyingcar_bringup bringup_rviz.launch
```
flyingcar_cartographer
```bash
# cartographer slam
roslaunch flyingcar_cartographer flyingcar_cartographerSLAM.launch  
# pure localization
roslaunch flyingcar_cartographer flyingcar_pure_localization.launch
roslaunch flyingcar_cartographer flyingcar_init_pose.launch
# monitor result by pc
roslaunch flyingcar_cartographer cartograher_rviz.launch
# save map as .pbstream
rosservice call /finish_trajectory 0
rosservice call /write_state "{filename: '${HOME}/Downloads/map.bag.pbstream'}"
```
test CSI camera with gstream, more test could be found at [JetsonHacksNano/CSI-Camera](https://github.com/JetsonHacksNano/CSI-Camera)
```bash
gst-launch-1.0 nvarguscamerasrc ! 'video/x-raw(memory:NVMM),width=3820, height=2464, framerate=21/1, format=NV12' ! nvvidconv flip-method=2 ! 'video/x-raw,width=960, height=616' ! nvvidconv ! nvegltransform ! nveglglessink -e
```
test realsense d435 on realsense-viewer (make sure that you use 3.0 port/wire, and the correct orientation of type-C)
```
cd /usr/local/bin
realsense-viewer 
```
