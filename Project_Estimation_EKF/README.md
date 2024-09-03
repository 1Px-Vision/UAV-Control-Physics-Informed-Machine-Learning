
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
PASS: ABS(Quad.GPS.X-Quad.Pos.X) was less than MeasuredStdDev_GPSPosXY for 67% of the time
PASS: ABS(Quad.IMU.AX-0.000000) was less than MeasuredStdDev_AccelXY for 69% of the time

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

* Implement the PredictState() function: ````/src/QuadEstimatorEKF.cpp```` line 180 to line 192.
* Implement the GetRbgPrime() function: ````/src/QuadEstimatorEKF.cpp```` line 216 to line 234.
* Implement the Predict() function:````/src/QuadEstimatorEKF.cpp```` line 277 to line 288.
 
These functions collectively handle all aspects of the prediction phase in the estimator. This step consists of two parts. First, we predict the state using the acceleration measurements. Without altering the code, we obtain the following data:

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_3_%20Estimador_1.jpg)

After implementing the first part, you can see the estimation drift:

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_3_%20Estimador_1_drift.jpg)


In the second part, we update the covariance matrix and finalize the EKF state using the equations from the Estimation for Quadrotors [paper](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Estimation_for_Quadrotors.pdf) provided by Udacity.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_3_Estimador_2.gif)

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_3_%20Estimador_2.jpg)

The red-dotted line represents the sigma, which does not change over time. After the update of the covariance matrix:

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_3_%20Estimador_2_Predictor.jpg)

**4. Implement the magnetometer update**

We need to update the state using the magnetometer measurements. Without modifying the code, we have the following data:

The magnetometer update is implemented at ````/src/QuadEstimatorEKF.cpp```` line 341 to line 353
````
PASS: ABS(Quad.Est.E.Yaw) was less than 0.120000 for at least 10.000000 seconds
PASS: ABS(Quad.Est.E.Yaw-0.000000) was less than Quad.Est.S.Yaw for 67% of the time
````

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_4_Magnetometer.gif)

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_4_Mag_1.jpg)

To implement the update, we need to use the Magnetometer equations from the Estimation for Quadrotors [paper](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Estimation_for_Quadrotors.pdf). Once implemented, we obtained the following data:

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_4_Mag_2.jpg)

**5. Implement the GPS update**

The final step before completing the EKF implementation is the GPS update. We have the following data after removing the ideal estimator from the code without any other modifications.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_5_GPS.gif)

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_5_GPS_1.jpg)

To implement this update, we need to use the equations GPS from the Estimation for Quadrotors [paper](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Estimation_for_Quadrotors.pdf). After it was implemented, we received this data:

````
PASS: ABS(Quad.Est.E.Pos) was less than 1.000000 for at least 20.000000 seconds
````

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_5_GPS_Control.gif)

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/Project_Estimation_EKF/Results/Scenario_5_GPS_2.jpg)

## Flight Evaluation
