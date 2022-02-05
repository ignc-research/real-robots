# Working with the TiaGo PAL robot
To start the robot, press the button for about 2 seconds and release. 

Due to some damage in the board computer, the robot isnt working out of the box. Normaly, it would start its own network for other devices to connect to. 
Hence, we are following another solution:

Plug a keyboard to the robot and press following combinations in order to start the PC: F1, F5, Enter.


Alternatively, plug a display into the onboard PC of the robot. Therefore, you have to plug the cable into the opening at the base of the robot. 


Once you have started the PC successfully, connect a remote PC via ethernet cable. Make sure that this remote pc is connected to both WIFI and the TIAGo via ethernet.
To make sure that the internet connection is not disrupted, go into the network settings of the Ethernet connection and delete the gateway field (For our setup,
everything is already set up correctly).


Use a second remote PC and connect via ssh to the first remote PC, inside that ssh connection, connect via ssh again to the Tiago robots PC:

```
ssh pal@10.68.0.1
pw:pal
```

Now you can control the robot via the second remote PC. All drivers are up and topics published. 
You can also set the ROS_MASTER_URI to avoid using ssh. 
