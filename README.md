<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/logo.png" width="600"/>
</p>


[![Last build check](https://img.shields.io/badge/build-pass%20--%20last%20check%2003%2F17-green.svg)]()
[![Platform](https://img.shields.io/badge/platfrom-%20ubuntu%20%7C%20mac%20-lightgrey.svg)]()

## What is it

This is an open source project that intended to help people create autonomous drone with [pixhawk](https://pixhawk.org/) controller.
we created the project in cpp and python to enable fast image processing and operating the drone in real time.
the project also include built in missions. our goal was to fly to specific GPS location scan the area for a 'bullseye target' and land on the center of the target.
you can use that framework to create your own missions.
the framework include api that helps transmit live video stream over wifi or/and record the video to file.<br>

We created that project in the [Geomatric Image Processing lab at the Technion](http://gip.cs.technion.ac.il/) . our goal was to create a simple framework to manage simple and complex missions represented by state machine

## Requirements

1. g++
    [install g++ for mac](http://www-scf.usc.edu/~csci104/20142/installation/gccmac.html)
    or install for ubuntu : apt-get install g++
2. [boost](http://www.boost.org/)  you can install it with apt-get in linux or with brew in mac.
3. [boost-python](http://www.boost.org/doc/libs/1_63_0/libs/python/doc/html/index.html) - to link cpp with python [check that github project for examples](https://github.com/TNG/boost-python-examples)
4. Opencv cpp - for image processing
    [you wont need the opencv for python because all the image processing is done with cpp code]
    you can install opencv on mac with 'port' or with brew or install with apt-get in linux.
5. [Armadillo](http://arma.sourceforge.net/) you can install it with apt-get in linux or with brew in mac.
6. [LAPACK — Linear Algebra PACKage](http://www.netlib.org/lapack/) you can install it with apt-get in linux or with brew in mac.
7. [BLAS - Basic Linear Algebra Subprograms](http://www.netlib.org/blas/) if you installed lapack you suppose to have blas check under /usr/lib
8. python
    [install python form mac](https://www.python.org/downloads/mac-osx/)
    or install for ubuntu : apt-get install python
9. pip - [python package manager](https://pip.pypa.io/en/stable/installing/)
10. python packages : 'websocket-client', 'websocket-server', 'enum34', 'dronekit', 'dronekit-sitl', 'numpy' run ```pip install -r requirements.txt``` in to project_root to install all
11. [nodeJs](https://nodejs.org/en/) + [http-server](https://www.npmjs.com/package/http-server) or any other way to simply create a localhost html server for displaying the UI

## Build

You can build the project by running ```sudo make``` in the root directory

## Components & Features

We divided the system to 3 main components:

1. vehicle - all the code that runs on the drone
2. ground - send commands to the drone and helps manage drone's missions.
3. common - common code that useful for both ground and vehicle

### Vehicle

If the installation process went smoothly you should have dronekit-sitl already installed.<br>
[dronekit-sitl](http://python.dronekit.io/develop/sitl_setup.html) is a program that simulate pixhawk controller
you can test if it works by running ``` dronekit-sitl copter ``` in the terminal it should look something like that:
<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/dronekit-sitl.jpg" width="600"/>
</p>

You can see that the simulator waiting for connection on port 5760 .<br>
now you can run the vehicle program.<br>
just run that in the project root directory:
```
python -m vehicle.python.main tcp:<ip where you run dronekit-sitl>:5760 <ip where you want to see the live video stream>
```
(we will explain later how to see the video stream)

For example if you run the code in the same pc that you run dronekit-sitl you can just run:

```
python -m vehicle.python.main tcp:127.0.0.1:5760 127.0.0.1
```
You should see something like that if you don't have camera connected:
<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/vehicle-connected-without-camera.jpg" width="600"/>
</p>

Or something like that if you do :
<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/vehicle-connected-with-camera.jpg" width="600"/>
</p>

### Ground

#### Communication

Now we need to create a communication between the vehicle(the drone) and the ground(the browser)
Run this code to establish the communication channel

```
python -m ground.main  <drone_ip>

```

For example if you run all in the same pc just run
```
python -m ground.main  127.0.0.1
```
It should look something like that :

<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/communication.jpg" width="600"/>
</p>

#### GUI - graphic user interface

We use html and javascript to display the state of the mission.

You can use http-server to display the GUI just follow those steps:

1. [Install nodeJs](https://nodejs.org/en/)
2. Installing nodeJs should install [npmJs](https://www.npmjs.com/) 'node package manager' use npm to install http-server by running ```npm install -g http-server``` in the terminal
3. navigate to droneproject/ground/ui and run http-server -p5555
4. naviagte to http://localhost:5555/ in chrome and open the [console](https://developers.google.com/web/tools/chrome-devtools/console/)
5. load the mission by running loadMission("FindAndLandMission")

It should Look like this when you run http-server from the terminal  :

<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/http-server.jpg" width="600"/>
</p>

If you already successfully established communication with the drone you ready to connect else just jump back to the [Communication](#communication) section.<br>
Connect the ui to the browser by following those steps:

1. Navigate to the [browser](http://localhost:5555) open the console and load the mission by running
loadMission("FindAndLandMission").
2. Connect to the drone by running client.connect().
3. Start the mission by running client.send('{"type" : "CMDTypes.FIND_AND_LAND_MISSION" , "body": {"alt":2, "distance": 100}}').

And after the mission start

<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/smwithcommands.jpg" width="600"/>
</p>

And then when you run the mission from the browser :

<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/state-machine.jpg" width="600"/>
</p>

**Note** : the 'Find and land' mission fits to our  specific drone . <br> the camera sits on the drone at specific rotation and we analyzing the image base on that and command the drone to move in order to minimize the distance to the center of the target.

## How To Get Started

To better understand the architecture of that project start by reading the [project's presentation](#project-presentation) that explains about all the components. <br>Then you can take a look on the missions that we already created under project_root/vehicle/cpp/src/mission. (for example understand how 'Up and down' mission works).<br>
finally you can try to create your own simple mission.

## Project presentation

Under ```project_root/presentation```<br>
you can find our final project presentation ,one version is pdf and one is power point .

## Videos

Find and land mission's video from the drone camera(without the go to GPS part)
after takeoff the drone start to scan the area and then decide where to go base on the image algorithm that tells him where is the center of the target.

**Click on the gif to see the full video**:

[![Flight 1](https://j.gifs.com/O7D9OY.gif)](https://www.youtube.com/watch?v=e26DO6jsaFM&feature=youtu.be)


[![Flight 2](https://j.gifs.com/Y6mY9n.gif)](https://youtu.be/AuR8C7m42ZQ)

## Useful links
* [Dronekit](http://python.dronekit.io/)
* [MavProxy](http://ardupilot.github.io/MAVProxy/html/index.html)
* [Mavlous](https://github.com/wiseman/mavelous)
* [APM planner](http://ardupilot.org/planner2/)
* [Mission planner](http://ardupilot.org/planner/index.html)
* [Calibrate the camera](http://docs.opencv.org/2.4/doc/tutorials/calib3d/camera_calibration/camera_calibration.html)
* [Calibrate the compose and the GPS](http://ardupilot.org/copter/docs/common-compass-calibration-in-mission-planner.html)

## Our Drone

<p align="center">
  <img src="https://raw.githubusercontent.com/galprz/DroneProject/master/images/communication.jpg" width="600"/>
</p>
