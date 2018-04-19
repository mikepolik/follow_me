# follow_me

Follow me function for an autonomus trolley using two laserscanners and mecanum wheels.
Includes drivers for a laserscanner, localization and mapping (hector_slam), leg tracking and collision avoidance.

Computer OS: Ubuntu 16.04

ROS distribution: Kinect

## Reading data from S300 laserscanner

Origin:

  Copyright (C) 2014
  
  Software Engineering Group
  
  RWTH Aachen University

1. Make sure S300 is connected to a 24 VDC supply and to the PC via USB
2. List USB devices: `ls ‑l /dev | grep ttyUSB` or `dmesg | grep tty` before you plug the USB
3. Remember the serial port of the laserscanner: ttyUSB0 or ttyUSB1?
4. Check if „ttyUSBx“ has the right value of x (0, 1 etc.) in following scripts and change if required:
```
catkin_ws/src/sicks300/include/serialcomm_s300.h
catkin_ws/src/sicks300/src/sicks300.cpp
```
5. `sudo chmod 666 /dev/ttyUSBx`  - x is 0 or 1
6. `cd ~/catkin_ws` 
7. `source devel/setup.bash`
8. `roscore`
9. In a new terminal (if the scripts have been changed): `catkin_make`
10. `source devel/setup.bash`
11. `rosrun sicks300 sick300_driver`
12. In a new terminal: `rostopic list` (the `/laserscan` topic should be there as well)
13. `rosrun rviz rviz`
14. In Rviz: Add -> By topic -> LaserScan
15. Global Options -> Fixed Frame -> use base_laser_link to check the functionality of the laser; Frame Rate = 300
16. For clearer visual representation: LaserScan Size = 0,05

The topic `/laserscan` is now ready to use.

## Localization and mapping

Currently implemented via hector_slam.

Origin:

  Copyright (c) 2011
  
  Stefan Kohlbrecher
  
  TU Darmstadt

Some parameters in the `/hector_mapping/launch/mapping_default.launch` were modified (-->) for the S300 laserscanner:

map_multi_res_levels = 2 --> 4

map_resolution = 0.050 --> 0.025

map_update_distance_thresh = 0.4 --> 0.8


In `/hector_mapping/include/hector_slam_lib/matcher/ScanMatcher.h`:

in lines 209-215:

searchDir = 0.2 --> 1.6

To publish a transform from base_link (laser mounting surface) to the trolley_frame (tool trolley rotational centre) open `/hector_mapping/launch/mapping_default.launch` and modify the args x, y and yaw of link_to_trolley node:

`<node pkg="tf" type="static_transform_publisher" name="link_to_trolley" args="x y 0 yaw 0 0 base_link trolley_frame 100"/>`

## Leg detection and tracking

**TODO**
