# Project Quadrotor Controller

In this project, you will implement and fine-tune a cascade PID controller for drone trajectory tracking. The theory behind designing the controller using a feed-forward strategy is detailed in the paper Feed-Forward Parameter Identification for Precise Periodic Quadrocopter Motions. The following diagram illustrates [the cascaded control loops of the trajectory-following controller](https://www.dynsyslab.org/wp-content/papercite-data/pdf/schoellig-acc12.pdf).

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/cascade_control_from_article.png)

## Project description

This project has two parts: the first requires implementing the controller in Python, while the second involves implementing it in C++.

## Python Implementation

Based on the initial project from the FCND, our task is to control a simulated drone using Python to fly in a square trajectory within a backyard environment. The controller functionality should be implemented in the ````controller.py```` class. 

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/python-simulator-1.gif)

Tuning the controller for this trajectory is challenging. Initially, it's difficult to predict the outcomes. The trajectory resembles something like this:

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/Trajectory.jpg)

When the scenario successfully passes the test, you will see this line in the standard output:

## C++ Implementation

This section represents the more complex aspect of the project. While parameter tuning in Python was challenging, the complexity here is higher in order of magnitude. The C++ implementation is just a small part of this demanding task. In this context, the simulator imposes more realistic constraints on the implementation, and issues can arise if these constraints are not handled correctly. Even more intriguing are the situations where the implementation is slightly off, not entirely incorrect. Udacity provides a seed project that includes the simulator implementation and placeholders for the controller code. The seed project’s README.md offers guidance on running the project and details the tasks required to implement the controller. There are five scenarios that we need to address. The simulator runs in a loop for each scenario and displays whether the scenario has passed on the standard output.

All the C++ code can be found in the /cpp directory. The more notable files include:

* **/cpp/config/QuadControlParams.txt:** This file holds the configuration settings for the controller. You can modify it while the simulator is running, and the simulator will update those parameters in the next execution loop.

* **/cpp/src/QuadControl.cpp:** This file is central to the project, as it contains the implementation of the controller. However, I should note that most of the effort required to pass the scenarios is spent on parameter tuning, rather than the code in this file.

### Scenario 1: Sensor noise

In this scenario, we modify the drone's mass in the /cpp/config/06_SensorNoise.txt file until it achieves brief hovering

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/Results/Result_scenario_1.gif)

````
PASS: ABS(Quad.GPS.X-Quad.Pos.X) was less than MeasuredStdDev_GPSPosXY for 67% of the time
PASS: ABS(Quad.IMU.AX-0.000000) was less than MeasuredStdDev_AccelXY for 69% of the time
````

### Scenario 2: Body rate and roll/pitch control

Now it's time to start coding. The GenerateMotorCommands method needs to be implemented by solving these equations:

$$
\begin{align*}
\quad F_1 + F_4 - F_2 - F_3 = \frac{n_x}{l} = \tau_x & (1) \\
\quad F_1 + F_2 - F_3 - F_4 = \frac{n_y}{l} = \tau_y & (2) \\
\quad F_1 - F_2 + F_3 - F_4 = -\frac{n_z}{k} = \tau_z & (3) \\
\quad F_1 + F_2 + F_3 + F_4 = n_z = \dot{t}_z & (4) \\
\end{align*}
$$

F_1 to F_4 represent the motor thrusts, while 𝜏_𝑥, 𝜏_𝑦, and 𝜏_𝑧 denote the moments about each axis. 𝐹_𝑡 is the total thrust, 𝜅 is the drag-to-thrust ratio, and 𝑙 is the drone arm length divided by the square root of two. These equations are derived from classroom lectures. It's important to note a couple of considerations: for instance, in NED (North-East-Down) coordinates, the z-axis is inverted, which explains why the moment about the z-axis is inverted here. Additionally, during implementation, it is observed that 𝐹_3 and 𝐹_4 are swapped. In other words, 𝐹_3 in the lectures corresponds to 𝐹_4 in the simulator, and vice versa. The next step involves implementing the BodyRateControl method using a proportional (P) controller along with the moments of inertia. At this stage, the **kpPQR** parameter needs to be fine-tuned to prevent the drone from flipping. However, before doing this, it's essential to command some thrust in the altitude control, as thrust is no longer commanded in the GenerateMotorCommands. A suitable value for thrust is thrust=mass×CONST_GRAVITY. Once this is completed, we proceed to the RollPitchControl method. This implementation requires applying a set of equations. A P controller should be applied to the 𝑅_13 and 𝑅_23 elements of the rotation matrix, which relate body-frame accelerations to world-frame accelerations.

$$
\begin{align*}
\quad p_c^c = k_p(p_c^b - p_c^c)  & (1)\\
\quad q_c^c = k_p(q_c^b - q_c^c) & (2)\\
\quad p_c^b = R_{13} & (3)\\
\quad q_c^b = R_{23} & (4)\\
\end{align*}
$$

But the problem is you need to output roll and pitch rates; so, there is another equation to apply:

$$
\quad 
\begin{pmatrix}
p_c \\
q_c 
\end{pmatrix}=
\frac{1}{R_{33}}
\begin{pmatrix}
R_{21} & -R_{11} \\
R_{22} & -R_{12}
\end{pmatrix}
\begin{pmatrix}
\ddot{x}_c \\
\ddot{y}_c 
\end{pmatrix}
$$

It is important to note that the received thrust needs to be inverted and converted to acceleration before applying the equations. Once the implementation is complete, you should begin tuning the **kpBank** and **kpPQR** parameters until the drone achieves a relatively stable upward flight.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/Results/Result_Scenario_2.gif)

When the scenario is passing the test, you should see this line on the standard output:

````
PASS: ABS(Quad.Roll) was less than 0.025000 for at least 0.750000 seconds
PASS: ABS(Quad.Omega.X) was less than 2.500000 for at least 0.750000 seconds
````

### Scenario 3: Position/velocity and yaw angle control

* **AltitudeControl:** This method utilizes a PD controller to regulate acceleration, which determines the thrust required to maintain the desired altitude.

$$
\begin{align*}
\quad 
\begin{pmatrix}
\ddot{x} \\
\ddot{y} \\
\ddot{z}
\end{pmatrix}
=\begin{pmatrix}
0 \\
0 \\
g
\end{pmatrix}
+
\frac{T}{m}
\begin{pmatrix}
R_{13} \\
R_{23} \\
R_{33}
\end{pmatrix}  & (1)\\
\quad \ddot{x}^c = R_{13} & (2) \\
\quad \ddot{y}^c = R_{23} & (3) \\
\quad \ddot{z}^c = R_{33} & (4) \\
\quad \ddot{z}_d = \ddot{z} - g & (5) \\
\quad c = \frac{\ddot{z}_d - g}{\ddot{z}^c} & (6) \\
\quad \ddot{z}_d = k_p (z - z_d) + k_d (\dot{z} - \dot{z}_d) + \ddot{z} & (7)
\end{align*}
$$

To test this, return to scenario 2 and verify that the drone remains stable and does not fall. In this scenario, the PID controller is set to be inactive, and the thrust should be calculated as the product of the mass and ````CONST_GRAVITY````.

* **LateralPositionControl:** This component uses a PID controller to regulate acceleration along the x and y axes.

* **YawControl:** This case is simpler, as it employs a P controller. It's recommended to keep the yaw angle within the range [−π,π] for optimization.

After implementing all the code, set the values of **kpYaw**, **kpPosXY**, **kpVelXY**, **kpPosZ**, and **kpVelZ** to zero. Take a deep breath, and begin tuning the controllers, starting with the altitude controller and moving on to the yaw controller. Be patient, as this process takes time.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/Results/Result_Scenario_3.gif)

When the scenario is passing the test, you should see this line on the standard output:

````
PASS: ABS(Quad1.Pos.X) was less than 0.100000 for at least 1.250000 seconds
PASS: ABS(Quad2.Pos.X) was less than 0.100000 for at least 1.250000 seconds
PASS: ABS(Quad2.Yaw) was less than 0.100000 for at least 1.000000 seconds
````

### Scenario 4: Non-idealities and robustness

This is an interesting scenario, we need to add an integral component to the altitude controller, transitioning it from a PD to a PID controller. When I did this, everything stopped working correctly, and I had to re-tune the system, starting from scenario -1. Remember, patience is a virtue. Take your time and try again.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/Results/Result_scenario_4.gif)

When the scenario is passing the test, you should see this line on the standard output:

````
PASS: ABS(Quad1.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
PASS: ABS(Quad2.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
PASS: ABS(Quad3.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
````
### Scenario 5: Tracking trajectories

This is the final mandatory scenario where the drone must follow a specific trajectory. This scenario will expose any remaining errors in your code and may require further parameter tuning. Keep in mind that there are comments within the controller methods about the limits that need to be enforced in the system. Implementing these limits is essential to successfully pass this scenario.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Building_a_Controller/Results/Result_Scenario_5.gif)

When the scenario is passing the test, you should see this line on the standard output:

````
PASS: ABS(Quad2.PosFollowErr) was less than 0.250000 for at least 3.000000 seconds
````

## Implemented Controller

**1. Body Rate Control:**

* Implemented in both Python and C++.
* In Python, this control is implemented as a proportional controller in the body_rate_control method within /python/controller.py, from lines 179 to 195.
* In C++, it is implemented in the ````QuadControl::BodyRateControl```` method located in /cpp/src/QuadControl, from lines 95 to 121.

**2.Roll Pitch Control:**

* Implemented in both Python and C++.
* In Python, this functionality is handled by the roll_pitch_controller method in /python/controller.py, covering lines 142 to 177.
* In C++, it is implemented in the ````QuadControl::RollPitchControl```` method found in /cpp/src/QuadControl, from lines 124 to 167.
  
**3.Altitude Control:**

* Implemented in both Python and C++.
* In Python, this control is managed by the altitude_control method in /python/controller.py, spanning lines 112 to 139.
* In C++, the implementation is in the ````QuadControl::AltitudeControl```` method within /cpp/src/QuadControl, from lines 169 to 212.

**4.Lateral Position Control:**

* Implemented in both Python and C++.
In Python, this control is implemented in the lateral_position_control method of /python/controller.py, from lines 93 to 124.
In C++, the corresponding method is ````QuadControl::LateralPositionControl````, located in /cpp/src/QuadControl, covering lines 215 to 267.


**5.Yaw Control:**
* Implemented in both Python and C++.
* In Python, the yaw_control method in /python/controller.py implements this functionality, from lines 197 to 214.
* In C++, it is handled by the ````QuadControl::YawControl```` method in /cpp/src/QuadControl, spanning lines 270 to 302.

**6.Motor Command Calculation:**

* Implemented in C++ to compute motor commands based on the commanded thrust and moments.
* This calculation is handled by the ````QuadControl::GenerateMotorCommands```` method in /cpp/src/QuadControl, from lines 58 to 93.

## Flight Evaluation

### The Python implementation meets the minimum flight performance metrics:

````
Maximum Horizontal Error:  1.896498169249138
Maximum Vertical Error:  0.636822964449316
Mission Time:  2.121786 seconds
Mission Success:  True
````

### The implementation pass scenarios 1 - 5 on the C++ simulator:

````
# Scenario 1
PASS: ABS(Quad.PosFollowErr) was less than 0.500000 for at least 0.800000 seconds
# Scenario 2
PASS: ABS(Quad.Roll) was less than 0.025000 for at least 0.750000 seconds
PASS: ABS(Quad.Omega.X) was less than 2.500000 for at least 0.750000 seconds
# Scenario 3
PASS: ABS(Quad1.Pos.X) was less than 0.100000 for at least 1.250000 seconds
PASS: ABS(Quad2.Pos.X) was less than 0.100000 for at least 1.250000 seconds
PASS: ABS(Quad2.Yaw) was less than 0.100000 for at least 1.000000 seconds
# Scenario 4
PASS: ABS(Quad1.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
PASS: ABS(Quad2.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
PASS: ABS(Quad3.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
# Scenario 5
PASS: ABS(Quad2.PosFollowErr) was less than 0.250000 for at least 3.000000 seconds

````
