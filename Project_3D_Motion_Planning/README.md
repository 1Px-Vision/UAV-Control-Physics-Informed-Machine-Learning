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

#### 3. Set grid start position from local position

Retrieve the current start position and use it as the initial point for our path-planning task. I have created a method called ````get_gridrelative_position()```` to facilitate the conversion from the local frame to the grid-relative frame, which is relative to the map center.
````
grid_start = get_gridrelative_position(curr_local_pos[0:2], offsets)
````

Previously, I stored the offsets obtained from the ````create_grid```` method to use in converting NED coordinates to grid coordinates
````
offsets = (north_offset, east_offset)
````

#### 4. Set grid goal position from geodetic coords
To enhance the flexibility of specifying the goal location, I introduced two program input parameters: goal_long and goal_lat.
````
goal_lon='290'
goal_lat='300'
goal_alt='-0.147'
drone = MotionPlanning(conn, goal_lon, goal_lat)
````

Next, I set the goal position using the input parameter values, converted these to NED coordinates, and then determined their position relative to the grid.
````
custom_goal_pos = (-122.398414, 37.7939265, 0)
goal_local = global_to_local(custom_goal_pos, self.global_home)[0:2]
grid_goal = get_gridrelative_position(goal_local, offsets)
````

#### 5. Modify A* to include diagonal motion (or replace A* altogether)

In the file ````planning_utils.py````, I updated the Action enumeration to incorporate additional actions along with their corresponding costs:
````
NORTH_WEST = (-1, -1, np.sqrt(2))
NORTH_EAST = (-1, 1, np.sqrt(2))
SOUTH_WEST = (1, -1, np.sqrt(2))
SOUTH_EAST = (1, 1, np.sqrt(2))
````
Furthermore, I incorporated the corresponding logic into the valid actions method:

````
# Diagonal Actions
if x - 1 < 0 or y - 1 < 0 or grid[x - 1, y - 1] == 1:
    valid_actions.remove(Action.NORTH_WEST)
if x - 1 < 0 or y + 1 > m or grid[x - 1, y + 1] == 1:
    valid_actions.remove(Action.NORTH_EAST)
if x + 1 > n or y - 1 < 0 or grid[x + 1, y - 1] == 1:
    valid_actions.remove(Action.SOUTH_WEST)  
if x + 1 > n or y + 1 > m or grid[x + 1, y + 1] == 1:
    valid_actions.remove(Action.SOUTH_EAST)
````

#### 6. Cull waypoints

I developed a path-pruning algorithm named prune_path, which utilizes a collinearity test to assess whether a set of points is collinear within a specified threshold.
````
def collinearity_test(p1, p2, p3, epsilon=1e-6):   
    m = np.concatenate((p1, p2, p3), 0)
    det = np.linalg.det(m)
    return abs(det) < epsilon
````
In the pruning algorithm, if the points were collinear, the middle point was eliminated from the plan. This was applied to the path generated by A*:
````
path = prune_path(path)
````




