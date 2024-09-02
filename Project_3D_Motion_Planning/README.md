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

I utilized the ````genfromtxt```` function from numpy to read the first line of the CSV file, then iterated over the data to populate a dictionary with the global home position. Subsequently, I updated the vehicle's global home position using the floating point values lat0 and lon0 with the set_home_position function. The code for this process is presented below:

````
data = np.genfromtxt('map/colliders.csv', delimiter=',', dtype=object, max_rows=1, autostrip=True, converters={0:converter, 1:converter})

global_home_pos = dict()
for d in data:
global_home_pos.update(d)

# Set home position to (lon0, lat0, 0)
self.set_home_position(global_home_pos['lon0']
                       global_home_pos['lat0'],
                       0.0)
print("Home Position Set: [", global_home_pos['lon0'], ", ",  global_home_pos['lat0'], "]")
````

#### 2. Set your current local position

The Udacidrone library includes a function that facilitates the conversion between local and global coordinates, and vice versa. In this case, I utilized the global_to_local function to transform the global coordinates sourced from the CSV file into a local coordinate system.

````
# Convert to current local position using global_to_local()
curr_local_pos = global_to_local(curr_global_pos, self.global_home)
````

#### 3. Set grid start position from local position
This modification enhances the flexibility of the vehicle's starting position. It enables the vehicle to begin its journey from its current location instead of always starting from the center of the map. To achieve this, I adjusted the grid_start to reflect the vehicle's current local position, subtracting any offsets for that axis. The relevant code is shown below:

````
# Define starting point on the grid based on current position rather than map center
grid_start = (int(curr_local_pos[0])-north_offset, int(curr_local_pos[1])-east_offset)
````

#### 4. Set grid goal position from geodetic coords
This step introduces flexibility in selecting the desired goal location. It enables the selection of any (latitude, longitude) within the map as the goal location and renders it on the grid. The initial action is to transform the existing coordinates into a global coordinate space before integrating the chosen (latitude, longitude) values. After updating the global goal position, the coordinates must be reverted to local coordinates for use in subsequent steps of the path planner. The corresponding code is presented below:

````
# Adapt to set goal as latitude / longitude position and convert
# Convert current local position to global coordinates
goal_coord = local_to_global([curr_local_pos[0], curr_local_pos[1], curr_local_pos[2]], self.global_home)

# Add latitude and longitude values to the goal location
goal_coord = (goal_coord[0] + 0.002, goal_coord[1], goal_coord[2] + 60)

# Convert back to local coordinates to send to the drone
goal_pos = global_to_local(goal_coord, self.global_home)
grid_goal = (int(goal_pos[0])-north_offset, int(goal_pos[1])-east_offset)
````

#### 5. Modify A* to include diagonal motion (or replace A* altogether)
To incorporate diagonal movement into the A* Search, I modified the planning_utils file by introducing four additional actions. These actions enable the vehicle to move diagonally across the grid at a cost of sqrt(2).

````
# diagonal directions
NORTH_WEST = (-1, -1, np.sqrt(2))
NORTH_EAST = (-1, 1, np.sqrt(2))
SOUTH_WEST = (1, -1, np.sqrt(2))
SOUTH_EAST = (1, 1, np.sqrt(2))
````

I also implemented validation checks to exclude diagonal actions from the list of valid actions when the vehicle is too near the edge of the map or an obstacle.

````
if x - 1 < 0 or y - 1 < 0 or grid[x - 1, y - 1] == 1:
	valid_actions.remove(Action.NORTH_WEST)
if x - 1 < 0 or y + 1 > m or grid[x - 1, y + 1] == 1:
	valid_actions.remove(Action.NORTH_EAST)
if x + 1 > n or y - 1 < 0 or grid[x + 1, y - 1] == 1:
	valid_actions.remove(Action.SOUTH_WEST)
if x + 1 > n or y + 1 > m or grid[x + 1, y - 1] == 1:
	valid_actions.remove(Action.SOUTH_EAST)
````

#### 6. Cull waypoints

The initial phase of eliminating waypoints involved developing a collinearity test. This test determines if three specified points are aligned linearly. The function used for this collinearity test is presented below:

````
def collinear(p1, p2, p3):
	collinear = False

	# Add points as rows in a matrix
	mat = np.vstack((point(p1), point(p2), point(p3)))
	
	# Calculate determinant of the matrix
	det = np.linalg.det(mat)

	# Collinear is true if the determinant is less than epsilon
	if det < epsilon:
		collinear = True

	return collinear

````
The following code snippet demonstrates path pruning through a collinearity check. If the three waypoints align in a straight line, the middle waypoint is removed from the list.

````
# Prune path to minimize number of waypoints
i = 0
# Loop through all waypoints in groups of three
while i < len(path) - 2:
	p1 = path[i]
	p2 = path[i+1]
	p3 = path[i+2]

	# If the points are collinear, remove the middle waypoint
	if collinear(p1, p2, p3):
		path.remove(path[i+1])
	else:
		i += 1

````


