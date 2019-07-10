# FlyingCar
A portable sensor pack with laser range scanner, IMU, CSI camera, RGBD camera and SBC, no encoder needed.  
ydlidar laser: typeC to USB3.0  
realsense d435: typeC to USB3.0  
Sparkfun imu m0: miniUSB to USB3.0  
ydlidar power: miniUSB to USB3.0   

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
