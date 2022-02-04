# Youbot
The youbot is only working with ROS Hydro and Ubuntu 12.04, which is a very old Linux distribution. Hence, it is recommended to use a remote PC to log into the youbot PC
via ssh, or install the packages directly on the youbot's pc. However, all necessary packages for navigation are already installed. The following guide will provide instructioins
on how to use the youbot. The youbot IP is: 192.168.117.23.

First connect to the youbot via ssh: 

    ssh youbot@192.168.117.23
    pw: youbot

Start the robot and its hokuyo laser scan, which is a 260° planar laser scanner:

    roslaunch youbot_driver_ros_interface youbot_driver.launch

## Teleoperation
Launch teleoperation node

    rosrun teleop_twist_keyboard teleop_twist_keyboard.py

Attention: the youbot is fast (up to 0.8m/s)! Please check thee velocity before you start pressing any keys! 
