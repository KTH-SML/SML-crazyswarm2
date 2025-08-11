* Local CI: [![ROS 2](https://github.com/IMRCLab/crazyswarm2/actions/workflows/ci-ros2.yml/badge.svg)](https://github.com/IMRCLab/crazyswarm2/actions/workflows/ci-ros2.yml)
* Rolling Dev CI : [![Build Status](https://build.ros2.org/job/Rdev__crazyflie__ubuntu_noble_amd64/badge/icon)](https://build.ros2.org/job/Rdev__crazyflie__ubuntu_noble_amd64/)
* Jazzy Dev CI: [![Build Status](https://build.ros2.org/job/Jdev__crazyswarm2__ubuntu_noble_amd64/badge/icon)](https://build.ros2.org/job/Jdev__crazyswarm2__ubuntu_noble_amd64/)
* Humble Dev CI: [![Build Status](https://build.ros2.org/job/Hdev__crazyswarm2__ubuntu_jammy_amd64/badge/icon)](https://build.ros2.org/job/Hdev__crazyswarm2__ubuntu_jammy_amd64/)


# Crazyswarm2
A ROS 2-based stack for Bitcraze Crazyflie multirotor robots, with the necessary configurations to make the setup easier here at SML.

The documentation is available here: https://imrclab.github.io/crazyswarm2/. Apart from help in our lab, you can ask for help in their [Discussion](https://github.com/IMRCLab/crazyswarm2/discussions) forum.

## Information

At SML, we are soon going to have 12 Crazyflies 2.1+ with LED ring deck. This repository contains all setup files necessary for using them.

## Installation

Follow the instructions of the [Crazyswarm2 library](https://imrclab.github.io/crazyswarm2/installation.html) up until step 2:

```
sudo apt install libboost-program-options-dev libusb-1.0-0-dev
pip3 install rowan nicegui
sudo apt-get install ros-$ROS_DISTRO-motion-capture-tracking
pip3 install cflib transforms3d
sudo apt-get install ros-$ROS_DISTRO-tf-transformations
```

If you have problems with installing the motion_capture_tracking package, you can install it from source in your workspace (see [motion_capture_tracking](https://github.com/IMRCLab/motion_capture_tracking))

Create a ros workspace and clone this repo
```
mkdir ~/crazyswarm_ws && cd ~/crazyswarm_ws
mkdir src && cd ./src

git clone git@github.com:KTH-SML/SML-crazyswarm2.git --recursive

# optionally
git clone git@github.com:IMRCLab/motion_capture_tracking.git
```

Build your ROS workspace
```
cd ..
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
```

Set up the Crazyradio:

For the crazyradio, you need to setup usb rules in order to communicate with the Crazyflie. Find the instructions for that here in [Bitcraze’s USB permission guide](https://www.bitcraze.io/documentation/repository/crazyflie-lib-python/master/installation/usb_permissions/) for Linux.

Set up software-in-the-loop simulation (optional): 

Check step 7 in the [installation instructions](https://imrclab.github.io/crazyswarm2/installation.html).

## Usage

For communicating with the Crazyflies, you can use
- The Crazywarm2 library for autonomous flying
- The CFClient with joystick (Attention: This requires some training. Also take care to not have a Crazyflie connected when choosing the appropriate keymapping - The drone might start flying before it should)
- The Crazyflie Android/iOS app - as the joystick solution, this is hard to control and only good for debugging

### Crazyswarm library

For general use, refer to the [official documentation](https://imrclab.github.io/crazyswarm2/index.html). 

TODO: 
- [ ] Have Bjarnes code as an example?

### Positioning

The library supports both position estimates from Qualisys directly, and own estimates using `librigidbodytracker`. 

The latter is used currently, as it supports multiple crazyflies with the same marker setup. Each crazyflie has a unique number on one of the arms. 
- Select the crazyflies that you are using in `crazyflies.yaml`.
- Select the starting positions for each crazyflie also in `crazyflies.yaml`.
The crazyflie must start within a couple of centimeters from the specified starting location, otherwise, they will not be found.

To use Qualisys positioning directly, the following needs to be changed:
1. In `crazyflies.yaml`, choose
   ```
   robot_types:
  	cf21:
      motion_capture:
	    tracking: "vendor" # instead of "librigidbodytracker"
   ```
2. In Qualisys, go to settings. Under `Processing`, check `Calculate 6DOF`. Confirm with `Apply`.
3. _Optional_: Depending on your maker setup, you might need to add it to Qualisys
4. Make sure that the name of the frame in Qualisys is the same as the drone name in `crazyflies.yaml`.
From there on, it works as before. You don't need to start the drone from a specified starting position any more, but each drone needs a unique marker arrangement.

### Radio channels

The standard address for each crazyflie is 0xE7E7E7E7E7, it can be changed in the [CFClient](https://www.bitcraze.io/documentation/repository/crazyflie-clients-python/master/userguides/userguide_client). Currently, they are set to

| Crazyflie No. | Radio channel |
|---------------|---------------|
| cf01          | 0xE7E7E7E701  |
| cf02          | 0xE7E7E7E702  |
| cf03          | 0xE7E7E7E703  |

etc. up until

| Crazyflie No. | Radio channel |
|---------------|---------------|
| cf12          | 0xE7E7E7E712  |

(Note: "10" to 12" are written as decimal numbers in the radio channel, not in hexadecimal - we have 12 crazyflies, not x12)

### Charging

The Crazyflie can directly be plugged into a USB port for charging, or use the USB charging stations. 
If we decide to buy additional batteries later, there are separate chargers for these available.
