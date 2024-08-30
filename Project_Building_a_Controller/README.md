# Project Quadrotor Controller

In this project, you will implement and fine-tune a cascade PID controller for drone trajectory tracking. The theory behind designing the controller using a feed-forward strategy is detailed in the paper Feed-Forward Parameter Identification for Precise Periodic Quadrocopter Motions. The following diagram illustrates [the cascaded control loops of the trajectory-following controller](https://www.dynsyslab.org/wp-content/papercite-data/pdf/schoellig-acc12.pdf).

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/cascade_control_from_article.png)

## Project description

This project has two parts: the first requires implementing the controller in Python, while the second involves implementing it in C++.

## Python Implementation

Based on the initial project from the FCND, our task is to control a simulated drone using Python to fly in a square trajectory within a backyard environment. The controller functionality should be implemented in the ````controller.py```` class. 

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/python-simulator-1.gif)
