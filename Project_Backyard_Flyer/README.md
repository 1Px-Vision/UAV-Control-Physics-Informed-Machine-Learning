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

To interact with both the drone simulator and a real drone, you will use the [UdaciDrone API](https://udacity.github.io/udacidrone/). This API facilitates communication between Python and the drone simulator, bridging your code and the drone's operations. A fundamental component of the API is the Drone superclass, which provides the necessary commands for controlling the drone in the simulator. It also enables you to register callbacks or listeners that respond to changes in the drone's attributes. The objective of this project is to create a subclass derived from the Drone class, implementing a state machine that can autonomously fly the drone in a box pattern. A starting point for this subclass is provided in the ````backyard_flyer.py```` file.

## Drone Attributes
The Drone class contains the following attributes that you may find useful for this project:

* ````self.armed````: boolean for the drone's armed state
* ````self.guided````: boolean for the drone's guided state (if the script has control or not)
* ````self.local_position````: a vector of the current position in NED coordinates
* ````self.local_velocity````: a vector of the current velocity in NED coordinates
  
For a detailed list of all of the attributes of the Drone class check out the [UdaciDrone API documentation](https://udacity.github.io/udacidrone/).

## Outgoing Commands
The UdaciDrone API's Drone class also contains function to be able to send commands to the drone. Here is a list of commands that you may find useful during the project:

* ````connect()````: Starts receiving messages from the drone. Blocks the code until the first message is received
* ````start()````: Start receiving messages from the drone. If the connection is not threaded, this will block the code.
* ````arm()````: Arms the motors of the quad, the rotors will spin slowly. The drone cannot takeoff until armed first
* ````disarm()````: Disarms the motors of the quad. The quadcopter cannot be disarmed in the air
* ````take_control()````: Set the command mode of the quad to guided
* ````release_control()````: Set the command mode of the quad to manual
* ````cmd_position(north, east, down, heading)````: Command the drone to travel to the local position (north, east, down). Also commands the quad to maintain a specified heading
* ````takeoff(target_altitude)````: Takeoff from the current location to the specified global altitude
* ````land()````: Land in the current position
* ````stop()````: Terminate the connection with the drone and close the telemetry log

## Message Logging
The telemetry data is automatically logged in ````Logs\TLog.txt```` for logs created when running python ````backyard_flyer.py````. Each row contains a comma-separated representation of each message. The first row is the incoming message type. The second row is the time. The rest of the rows contain all the message properties. The types of messages relevant to this project are:

* ````MsgID.STATE````: time (ms), armed (bool), guided (bool)
* ````MsgID.GLOBAL_POSITION````: time (ms), longitude (deg), latitude (deg), altitude (meter)
* ````MsgID.GLOBAL_HOME````: time (ms), longitude (deg), latitude (deg), altitude (meter)
* ````MsgID.LOCAL_POSITION````: time (ms), north (meter), east (meter), down (meter)
* ````MsgID.LOCAL_VELOCITY````: time (ms), north (meter), east (meter), down (meter)

## Included in this repository 

* The code used backyard_flyer.py containing the challenge for this project
* File Test_drone_U.ipynb code using read to file TLog.txt with information position and velocity
* This README.md file

