# networking
## Network Setup for a Single Mobile Robot


### Assumptions
This document assumes you are working with one or more computers on a single robot, each running Ubuntu 18.04 with ROS melodic installed.  You may also have one or more sensors that communicates with a computer via ethernet cable.

### Overview
This document will give a brief description of how to set up ethernet profiles to network pairs of devices as outlined below.  It covers those situations marked by "y", but not those marked by "n".  It also gives reference to repositories with more concrete examples of what is discussed herein.

- [y] A companion computer with ethernet connection to a mobile platform computer.
- [y] A companion computer with ethernet connection to a sensor.
- [n] A companion computer with FTDI, USB, or other non-ethernet connection to a mobile platform.
- [n] A companion computer with non-ethernet (e.g. USB) to a sensor.

### 1. Networking a Companion and Mobile Platform Computer
If your mobile platform is not going to be configured to run on a single computer, you will likely need the mobile platform (which handles low level operations like motor commands) and the companion computer (which coordinates sensor data, and algorithms through ROS nodes) to talk to each other.  Assuming both computer have the requisite hardware, it is possible to do this wired, or wirelessly.  There may be a number of solutions for both scenarios, here we are going to cover best practices for the wired, ethernet connection.

Both your onboard and companion computers should have at least one ethernet port.  Since your companion computer sits between all other peripherals and the onboard computer, the onboard's single ethernet port is what you should use in making this connection.  On the other end of your Cat 5 cable will either be the companion's ethernet port, or the port on an ethernet to USB adapter.  If there are no other peripherals to be connected to the companion by ethernet, it is fine to connect directly to the companion's ethernet port.  If there is only one built in port, and a [LiDAR](https://github.com/westpoint-robotics/usma_LiDAR) or similar need to be connected, the sensor should get the direct port, and the computer-to-computer connection should use an ethernet to USB adapter. [INSERT PICTURE HERE]

Now that you know what your ethernet cable will be pluggin into, you will need to create a Wired Network Profile on the companion computer.
1. Open your Network Settings, and make sure you have "Network" selected from your options on the left; this is the label used for ethernet.
2. By the **Wired** heading, click the <kbd>+</kbd> symbol to add a new profile.
3. Under the "Identity" tab, name the profile something relevant to the mobile platform you're using (e.g. jackal_wired), and select the MAC Address for the port you are using for the connection.  If you do not know which port this is, make sure both computers are powered on and connected by Cat 5, and on your companion computer, run, `ifconfig` in a terminal.  If you unplug the onboard computer, and run `ifconfig` again, you should notice a port disappear, or change.  Select that port under the MAC Address dropdown.  This will force that port to be used for the connection of the onboard computer to the companion.  Plugging in to any other port will mean the onboard computer does not show up in a list of available IP Addresses.
4. Under the "IPv4 tab" select "Manual" instead of "Automatic (DHCP)".  Enter an address with the first three octets matching the broadcat address of your mobile platform (e.g. 192.168.131 for the Clearpath Jackal), and the last octet having a value between 0 and 200.  Your robot's user guide may have a suggestion about this, or there may be an example in the documentation in one of the repositories referenced below.  Use a Netmask 255.255.255.0, leaving the last octet free to be modified by the onboard computer.
5. Click "Add" to save the profile, and add it to the list of Wired network profiles.

### 2. Networking a Companion Computer and a Sensor
Your sensor should be plugged directly into an ethernet port on your companion computer if possible; consult your sensor's documentation, this may be required, or at least recommended.  Once your sensor is plugged in and powered on, the steps are essentially the same as for creating a wired network profile for the onboard computer.

1. Open your Network Settings, and make sure you have "Network" selected from your options on the left; this is the label used for ethernet.
2. By the **Wired** heading, click the <kbd>+</kbd> symbol to add a new profile.
3. Under the "Identity" tab, name the profile something relevant to the sensor you're using (e.g. velodyne_wired), and select the MAC Address for the port you are using for the connection.  If you do not know which port this is, run `ifconfig` in a terminal.  If you unplug the sensor, and run `ifconfig` again, you should notice a port disappear, or change.  Select that port under the MAC Address dropdown.  This will force that port to be used for the connection of the sensor to the companion.  Plugging in to any other port will mean the sensor does not show up in a list of available IP Addresses.
4. Under the "IPv4 tab" select "Manual" instead of "Automatic (DHCP)".  Enter an address with the first three octets matching the broadcat address of your sensor (e.g. 169.254.156 for the Ouster OS1 series LiDAR), and the last octet having a value between 0 and 200.  **Please consult the sensor's user guide, or one of the repositories referenced below.**  Use a Netmask 255.255.0.0, leaving the last two octets free to be modified by the sensor.
5. Click "Add" to save the profile, and add it to the list of Wired network profiles.

### 3. Reference and Example Repositories
- [Ouster LiDAR](https://github.com/westpoint-robotics/usma_LiDAR/tree/master/usma_ouster)
- [Velodyne LiDAR](https://github.com/westpoint-robotics/usma_LiDAR/tree/master/usma_velodyne)
- [Clearpath Jackal](https://github.com/westpoint-robotics/usma_jackal)
- [GVRBot](https://github.com/westpoint-robotics/usma_gvrbot)


### TODO:
- [ ] Include instructions for WiFi connections between computers.

