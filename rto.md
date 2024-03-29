# Robotino (RTO)
This guide was specificially written for the Robotino2 in the Telekom Innovation Lab. Hence, all IPs, configurations, etc. are matched to work with this specific robot and its equipment. Everything needed to run the robot and publishing laser scan data is already installed. Thus, to use the robot, skip the Installation and go to the Usage part. However, you can use this guide to set up new Robotinos with Sick Laser Scanners from scratch.

## Setup Robotino Hardware from scratch
[Source](https://github.com/dietriro/rto_core)

## Install Ubuntu 20.04 and ROS


In order to use the robot you first have to install Ubuntu Focal (20.04) using a bootable USB Stick. 

Then, install ROS Noetic.

```bash
#!/bin/bash

# Define config variables
ROS_DISTRO=noetic 
ROS_OS=ubuntu:focal
ROS_CI_DESKTOP=`lsb_release -cs`  # e.g. [trusty|xenial|...]

# Install ROS and all necessary dependencies
sudo sh -c "echo \"deb http://packages.ros.org/ros/ubuntu $ROS_CI_DESKTOP main\" > /etc/apt/sources.list.d/ros-latest.list"
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
wget -qO - http://packages.openrobotino.org/keyFile | sudo apt-key add -
sudo sh -c "echo \"deb http://packages2.openrobotino.org focal main\" > /etc/apt/sources.list.d/openrobotino.list"
sudo apt update -qq
sudo apt install -y dpkg rec-rpc robotino-dev robotino-api2 
sudo apt install -y git python3-pip python3-catkin-pkg python3-rosdep ros-$ROS_DISTRO-ros-base

# Install catkin tools using either one of these methods (only one works ususally, try it out in the worst case)
sudo apt install -y python3-catkin-tools
# pip3 install --user git+https://github.com/catkin/catkin_tools.git

# Source the just installed ROS environment and initialize rosdep
source /opt/ros/$ROS_DISTRO/setup.bash
sudo rosdep init
rosdep update

# Create a catkin workspace with the package under integration.
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
catkin_init_workspace

# Create the devel/setup.bash (run catkin_make with an empty workspace) and
# source it to set the path variables.
cd ~/catkin_ws
catkin build
source devel/setup.bash

# Download the code for rto_core
cd ~/catkin_ws/src
git clone https://github.com/dietriro/rto_core.git

# Install dependencies using rosdep
cd ~/catkin_ws
rosdep install -y --from-paths src --ignore-src --rosdistro $ROS_DISTRO --os=$ROS_OS

# Build the workspace
source /opt/ros/$ROS_DISTRO/setup.bash
cd ~/catkin_ws
catkin build
```

After finishing the setup and sucessfully building the workspace, you need to either source the just built workspace everytime you use it or you just add a line to your `.bashrc` so that it is source everytime a new console is opened. I recommend appending the following lines to the end of your `~/.bashrc` file:


```bash
# ROS Setup
source ~/catkin_ws/devel/setup.bash
# In case you want to set a different machine as the ros-master, set the ROS_MASTER_URI as well as ROS_IP to enable communication
export ROS_MASTER_URI='http://host-ip:11311'      
export ROS_IP='your-ip'                             

export ROBOT=rto-1              # Change to whatever robot you would like to use
export ROBOT_ENV=sim-simple     # Change to whatever world you would like to use
```

## Install hardware drivers for RTO on noetic
Next, you need to install the hardware drivers required to operate the real rto robot. [Source](https://wiki.openrobotino.org/index.php?title=Robotino_OS)
```
wget -qO - http://packages.openrobotino.org/keyFile | sudo apt-key add -
sudo su
echo "deb http://packages2.openrobotino.org focal main" > /etc/apt/sources.list.d/openrobotino.list
exit
sudo apt --fix-broken install
```
Then go to this page and download all packages 
https://wiki.openrobotino.org/index.php?title=Robotino_OS

go to your download folder and install them:
```
sudo dpkg -i *.deb
```
## Laser Scan Setup
We utilize the SICK Laserscanner, which is a 265° 3D lidar scanner. Using the cloud to scan package, we convert it to a 2D planar scan. 
Connect the Scanner to the robotino power source as depicted in this image:
Currently, the IP adress is set to 
```
192.168.1.20
```
It can be changed using the SOPAS app on a seperate Windows machine (as its only running on Windows), or using this launch file:

```
roslaunch sick_scan sick_new_ip.launch hostname:=192.168.1.20 new_IP:=192.168.1.xxx
```
If connecting to a new robotino, you have to set a static IP for the Ethernet Adapter. Therefore, go to the Ethernet settings and assign a static IP with the same namespace, that was given using the SOPAS app (for our Scanner, it is 192.168.1.xxx). Afterwards, the laserscanner should be recognized by the robotino. To check, use the Python script sick_scan/tools/sick_generic_device_finder/sick_generic_device_finder.py, which sends a UDP broadcast to which all available scanners respond with a device description. The varibale UDP_IP = "192.168.0.255" defines the broardcast adress used by the script. If you are using a different IP address configuration on your host pc you have to change this variable according to the brodcast address of your network card.
ifconfig shows the broadcast adress for every network adapter.


# Usage
If you have downloaded everything according to the previous steps, you can start the robot by using the following command:

    cd /home/robotino/code/real_ws/
    source devel/setup.zsh
    roslaunch rto_bringup robot.launch

In order to run this successfully, you first need to specify the robot you'd like to use (e.g. rto-1) as well as the world you would like the robot to use (e.g. sample) as environment variables, if you haven't added them to your `.bashrc` yet.

    export ROBOT=rto-1
    export ROBOT_ENV=sample
    
(This is already set in the zshrc)

The `robot.launch` file then starts up all necessary nodes for communicating with the robot daemons (for driving commands and odometry information, sick-scanner), for retrieving sensor information as well as moving the robot around using a joystick (in case it is connected). 

### Mapping

To record a map, start the gmapping node and provide it with the laser scan data (in our case /scan_new):
```
rosrun gmapping slam_gmapping scan:=scan_new   
```
In case you encounter problems due to gmapping not registering any scans, you can change the laser scan topic in the pointcloud2scan file (/home/robotino/code/real_ws/src/pointcloud_to_laserscan/src/pointcloud_to_laserscan_node.cpp) and name it instead /scan, which is the default topic, which gmapping listens to.

###Start teleoperation
Start teleoperation and drive around.
```
roslaunch rto_teleop keyboard_teleop.launch  
```
RTO teleoperation is limited to 0.1m/s, you may better use another teleoperation package with a higher speed or modify the package.

Save map with map server
```
rosrun map_server map_saver [--occ <threshold_occupied>] [--free <threshold_free>] [-f <mapname>] map:=/your/costmap/topic
```

### Navigation with a map
If you already have a map and want the robot to **localize and navigate** in it then you need to run the following:

    roslaunch rto_navigation navigation.launch

Again, the same environment variables as before (ROBOT, ROBOT_ENV) are needed. 

Regardless of the localization/navigation launched, you can always run an **rviz** instance with pre-designed configuration file

    roslaunch rto_bringup rviz.launch

This configuration includes visualizations for the most important topics.

## Troubleshouting
If you encounter problems with the urdf or xacro files not being found, convert the xacro files to urdf models.
```
rosrun xacro xacro model.xacro > model.urdf
```
Or change line 11 of catkin_ws/src/rto_core/rto_bringup/launch/components/base.xml 
```
from <param name="robot_description" textfile="$(find rto_description)/urdf/robots/$(arg robot).urdf" />
to
<param name="robot_description" textfile="$(find rto_description)/urdf/robots/$(arg robot).urdf.xacro" />
```

