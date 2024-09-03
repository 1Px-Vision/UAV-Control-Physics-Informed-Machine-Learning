
# Project 3D Estimation

## Overview

The 3D Estimation project is the fourth project in the Udacity Flying Car Nanodegree program. In this project, the objective was to develop an estimator using an Extended Kalman Filter (EKF) to fuse data from various sensors (GPS, magnetometer, and IMU) on a simulated quadrotor to estimate the vehicle's state in three dimensions.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/EstimationProjectHeadingDroneImage.png)

The project utilizes the following sensors:

* **GPS:** Provides 3D position and 3D velocity data.
* **IMU (accelerometer and gyroscope):** Measures 3D acceleration and angular rotation rates.
* **Magnetometer:** Measures the heading or yaw angle

In addition to the Extended Kalman Filter, a complementary filter is used to track the Roll and Pitch angles. The implementation of the EKF and Complementary Filter, including the framework, mathematical formulas, and pseudocode, is primarily based on concepts outlined in the paper Estimation for Quadrotors.

## Project description

### Implement Estimator

**1. Determine the Standard Deviation of Measurement Noise for GPS X Data and Accelerometer X Data:**
In this step, we run the Sensor Noise scenario to collect data from the GPS and IMU accelerometer. We then calculate the standard deviation of the measurement noise for both the GPS X and accelerometer X data.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_1_Sensor_Noise.gif)

````
GPS X Std: 0.7041748752871024
Accelerometer X Std: 0.5021182612424959
````


**2. Implement an improved rate gyro attitude integration scheme in the UpdateFromIMU() function:**. I utilized the Quaternion class along with the 
````IntegrateBodyRate()```` method to perform the integration of body rates. Afterward, I converted the results back to Euler angles using 
````estAttitude.ToEulerRPY()````.

````
PASS: ABS(Quad.Est.E.MaxEuler) was less than 0.100000 for at least 3.000000 seconds
````

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_2_%20Attitude_Estimation.gif)

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_2_%20Attitude_Estimation.jpg)

We need to implement a non-linear approach to achieve better results. First, we need to determine the derivatives for roll, pitch, and yaw using the following control equation. Once we have the derivatives, we can multiply them by ````𝑑𝑡```` to approximate the integral. Below is a more detailed graph following the non-linear integration:

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_2_%20Attitude_Estimation_error.jpg)
**3. Implement the Prediction Step for the Estimator**

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_3_Estimador_1.gif)

To complete this step, follow these procedures:

* Implement the PredictState() function.
* Implement the GetRbgPrime() function.
* Implement the Predict() function.
  
These functions collectively handle all aspects of the prediction phase in the estimator.


After implementing the first part, you can see the estimation drift:


This step consists of two parts. First, we predict the state using the acceleration measurements. Without altering the code, we obtain the following data:


In the second part, we update the covariance matrix and finalize the EKF state using the equations from the Estimation for Quadrotors [paper](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Estimation_for_Quadrotors.pdf) provided by Udacity.


**4. Implement the magnetometer update**

**5. Implement the GPS update**

## Flight Evaluation
