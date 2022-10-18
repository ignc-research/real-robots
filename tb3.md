ROS tutorial 
0.)General ROS:
To install packages for Ros therre are two ways:

install via apt-get install ros-distro-package…. then its under /opt/ros/distro/shareed/…. you might have to source /opt/setup.bash 

orr install from source with github repo

navigate to catkin/src
git clone your repo, and branch (git clone -b <branch>)
go out of src and run catkin_make
source devel/setup.bash

1)Setup Turtlebot3:
1.1)Basics: setup the communication between TB and remotet PC:
Remote PC: on all windows set:

export ROS_MASTER_URI=http://192.168.117.119:11311 (ip of your remote pc) 
export ROS_HOSTNAME=192.168.117.119 (ip of your remote pc)

then run :
roscore 

On turtlebot3: 

export ROS_MASTER_URI=http://192.168.117.119:11311 (ip of your remote pc) 
export ROS_HOSTNAME=192.168.117.49 (ip of turtlebot)

then run
roslaunch turtlebot3 turtlebot3_bringup.launch


On Remote PC: 
open new terminal, and 
export ROS_MASTER_URI=http://192.168.117.119 (ip of your remote pc) 
export ROS_HOSTNAME=192.168.117.119 (ip of your remote pc)

run 

roslaunch turtlebot3 turtlebot3_bringup_remote.launch

to check if both are connected:
open new terminal, and 
export ROS_MASTER_URI=http://192.168.117.119 (ip of your remote pc) 
export ROS_HOSTNAME=192.168.117.119 (ip of your remote pc)

run
rostopic list —> should show Turtlebot messages like scan, cmd_vel, etc..
export Turtlebot3 model = burger
rosrun rviz rviz —> to visualize thee topics
or roslaunch turtlebot_teleop

to not always run export commands, include them into .bashrc at the bottom
vim ~/.bashrc

export ROS_MASTER_URI=http://192.168.117.119 (ip of your remote pc) 
export ROS_HOSTNAME=192.168.117.119 (ip of your remote pc)

1.2.)Turtlebot Navigation:
Follow tutorials on https://emanual.robotis.com/docs/en/platform/turtlebot3/slam/#ros-1-slam 
1. create a map with SLAM: 
    1. export your Turtlebot model in our case: export TURTLEBOT3_MODEL=burger
    2. run SLAM gmapping: roslaunch turtlebot3_slam turtlebot3_slam.launch slam_methods:=gmapping
    3. run teleoperation node : roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch
    4. drive around and save map: rosrun map_server map_saver -f ~/map
2. run tb navigation node: 
    1. roslaunch turtlebot3_navigation turtlebot3_navigation.launch map_file:=$HOME/map.yaml (wherre you specify the paath to .yaml file of the map you just saved)
    2. run amcl node for localization
    3. manually specify position of the turtlebot inside the map using the green arrow at the top
    4. drive around back and forrth to get better position estimate
    5. visualize everything in RVIZ (if not done already), map (map), robot (urdf), scan (scan), location (amcl) by adding the parameters and setting the topics for them at the left, fixed frame should be set to map topic (default is odom)
    6. specify goal with greren arrow inside map, robot should cacluate path and navigate
