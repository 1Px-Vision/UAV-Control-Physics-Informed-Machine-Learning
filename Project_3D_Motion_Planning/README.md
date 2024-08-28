# Project 3D Motion Planning

It involves planning and executing a drone's trajectory in an urban environment. Building upon the event-based strategy used in the initial project, this work explores the complexities of path planning within a 3D environment. The code interfaces with the Udacity FCND Simulator using the [Udacidrone](https://udacity.github.io/udacidrone/) API.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_3D_Motion_Planning/drone_flying.gif)

## Project Details

### Explain the Starter Code
#### 1. Explain the functionality of what's provided in ````motion_planning.py```` and ````planning_utils.py````

Compared to the ````backyard flyer```` implementation, the ````motion_planning.py```` script introduces an additional Planning state. This state is managed through an event handler named plan_path. This method encompasses several key functionalities:

* It sets the flight state to Planning.
* It specifies a hardcoded target altitude and safety distance.
* It generates a grid representation of the configuration space, incorporating the predefined altitude and safety margins.
* It loads obstacle data from a file.
* It defines specific start and goal positions.
* It employs the A* algorithm to navigate the configuration space, using a given heuristic as the cost function to establish a path from the start to the goal state.
