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

The ````planning_utils.py```` module comprises several utility functions utilized by the path_plan method. These functions include:

* **create_grid:** Generates a 2.5D grid representation based on obstacle data (similar to the format in 'colliders.csv'), considering the drone's altitude and a specified safety distance.
* **Action:** An enumeration class that defines a three-element tuple for each of the four cardinal directions on a grid (North, South, East, West). Each element of the enumeration specifies a grid-based position change (N, E) and an associated cost.
valid_actions: Identifies all possible actions from a given grid cell, based on the grid configuration and the current node's position.
* **a_star:** Implements the A* pathfinding algorithm for a grid-based layout, using specified start and goal positions, a chosen heuristic, and the grid.
* **heuristic:** Provides a cost heuristic used by the A* algorithm to estimate the relative cost between two points.

### Implementing Your Path Planning Algorithm
#### 1. Set your global home position

I developed a function named ````get_latlon_fromfile```` which reads the first line of ````colliders.csv```` to extract the latitude and longitude of the initial start position in the geodetic frame. After retrieving these values, I converted them into floating-point numbers.
````
lat0, lon0 = get_latlon_fromfile('colliders.csv')
````
Next, I use those values to establish the current home position by calling

````
self.set_home_position(lon0, lat0, 0)
````

#### 2. Set your current local position

To determine the local position, which is relative to a pre-established global home position, I'll first need to obtain the current global position in the geodetic frame.
````
curr_global_pos = (self._longitude, self._latitude, self._altitude)
````
I then transform the global position into the local ECEF frame using the ````global_to_local()```` method, which is imported from ````planning_utils.py````
````
curr_local_pos = global_to_local(curr_global_pos, self.global_home)
````



