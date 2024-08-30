# Project Quadrotor Controller

In this project, you will implement and fine-tune a cascade PID controller for drone trajectory tracking. The theory behind designing the controller using a feed-forward strategy is detailed in the paper Feed-Forward Parameter Identification for Precise Periodic Quadrocopter Motions. The following diagram illustrates [the cascaded control loops of the trajectory-following controller](https://www.dynsyslab.org/wp-content/papercite-data/pdf/schoellig-acc12.pdf).

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/cascade_control_from_article.png)

## Project description

This project has two parts: the first requires implementing the controller in Python, while the second involves implementing it in C++.

## Python Implementation

Based on the initial project from the FCND, our task is to control a simulated drone using Python to fly in a square trajectory within a backyard environment. The controller functionality should be implemented in the ````controller.py```` class. 

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/python-simulator-1.gif)


## C++ Implementation

This section represents the more complex aspect of the project. While parameter tuning in Python was challenging, the complexity here is higher in order of magnitude. The C++ implementation is just a small part of this demanding task. In this context, the simulator imposes more realistic constraints on the implementation, and issues can arise if these constraints are not handled correctly. Even more intriguing are the situations where the implementation is slightly off, not entirely incorrect. Udacity provides a seed project that includes the simulator implementation and placeholders for the controller code. The seed project’s README.md offers guidance on running the project and details the tasks required to implement the controller. There are five scenarios that we need to address. The simulator runs in a loop for each scenario and displays on the standard output whether the scenario has passed or not.

