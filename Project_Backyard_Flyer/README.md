# Project: Backyard Flyer

## Udacity Flying Car Nanodegree - Term 1, Project 1: Backyard Flyer

This project is part of the first term of Udacity's Flying Car Nanodegree program. The objective is to control a simulated drone using Python to navigate in a square trajectory within a backyard setting. The Python code utilizes the Udacidrone API to interface with the simulator, which communicates using the MAVLink protocol.

The goals / steps of this project are the following:

* Program a quadrotor to fly autonomously in a box using Python.
* Use event-driven programming to program a quadrotor's flight computer.
* Become familiar with the DroneAPI for sending and receiving data from the drone.

### Project Overview

This project involves developing a state machine using event-driven programming to autonomously control a quadcopter within a Unity simulator. The Python code mimics the control operations typically performed from a ground station computer or an onboard flight computer. By leveraging MAVLink communication, this code can be adapted with minimal modifications to control a PX4 quadcopter autopilot.

#### Step 1: Download the Simulator
You can download the Udacity FCND Simulator from this [link](https://github.com/udacity/FCND-Simulator-Releases/releases). 

#### Step 2: Set up your Python Environment
If you haven't already, set up your Python environment and get all the relevant packages installed using Anaconda following the instructions in [this repository](https://github.com/udacity/FCND-Term1-Starter-Kit)

#### Task

The objective is to command the drone to fly in a square pattern, covering 10 meters on each side at an altitude of 3 meters. This flight path will be executed in two modes: first using manual control, and then using autonomous control. For manual control, follow the instructions provided with the simulator. Autonomous control will be implemented using an event-driven state machine. Each callback function will evaluate the transition criteria based on the current state. When these criteria are met, the system will transition to the next state, issuing the necessary commands to the drone. A visualization of this final flight plan is shown in the gif below:

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Backyard_Flyer/backyard_square.gif)

## Drone API

To interact with both the drone simulator and a real drone, you will use the UdaciDrone API. This API facilitates communication between Python and the drone simulator, bridging your code and the drone's operations. A fundamental component of the API is the Drone superclass, which provides the necessary commands for controlling the drone in the simulator. It also enables you to register callbacks or listeners that respond to changes in the drone's attributes. The objective of this project is to create a subclass derived from the Drone class, implementing a state machine that can autonomously fly the drone in a box pattern. A starting point for this subclass is provided in the backyard_flyer.py file.

## Included in this repository 

* The code used to Camera based 2D feature tracking on src directory with MidTermProject_Camera_Student.cpp, dataStructures.h, matching2D.hpp, matching2D_Student.cpp file containing the challenge for this project
* Result File with image Near-side outlier, Far-side outlier, and Multiple-side outlier
* results.csv test FP.6
* This README.md file

