# Project 3D Motion Planning

It involves planning and executing a drone's trajectory in an urban environment. Building upon the event-based strategy used in the initial project, this work explores the complexities of path planning within a 3D environment. The code interfaces with the Udacity FCND Simulator using the [Udacidrone](https://udacity.github.io/udacidrone/) API.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_3D_Motion_Planning/drone_flying.gif)

## Project description
The following is the main code used on the project:

* motion_planning_from_seed_project.py: This is the based implementation for this project provided by Udacity on its seed project.
* planning_utils_from_seed_project.py: It was also provided by Udacity on its seed project. It contains an implementation of the A* search algorithm and utility functions.
* motion_planning.py: This version extends the provided implementation with the following features:
   * The global home location can be found in the colliders.csv file.
   * The goal location is set from command line arguments (--goal_lat, --goal_lon, --goal_alt).
   * The calculated path is pruned with a collinearity function to eliminate unnecessary waypoints planning_utils.py: This file is used by motion_planning.py instead of the seed version. It supports the features 
 mentioned above and extends the A* search algorithm to include diagonal actions.


