# Youbot
The youbot is only working with ROS Hydro and Ubuntu 12.04, which is a very old Linux distribution. Hence, it is recommended to use a remote PC to log into the youbot PC
via ssh, or install the packages directly on the youbot's pc. However, all necessary packages for navigation are already installed. The following guide will provide instructioins
on how to use the youbot. The youbot IP is: 192.168.117.23.

## 1. Start the Robot
First, press on the On/Off button once and wait for the internal pc to start up. After a few seconds, press and hold the button again until it shows "start motor" on the display of the robot. Release the On/Off button. The robot will take about a minute to fully start. You could then plug a monitor, mouse, and keyboard to work directly on the youbot. However, it is recommended to use a remote PC as everything is already installed. 

## 2. Connect a Remote PC
Therefore, connect to the youbot via ssh: 

    ssh youbot@192.168.117.23
    pw: youbot


## 3. Start the Robot Drivers and Laser
Start the robot and its hokuyo laser scan, which is a 260° planar laser scanner:

    roslaunch youbot_driver_ros_interface youbot_driver.launch

## 4. Teleoperation
Launch teleoperation node

    rosrun teleop_twist_keyboard teleop_twist_keyboard.py

Attention: the youbot is fast (up to 0.8m/s)! Please check thee velocity before you start pressing any keys! 
