# Estimation Project #

This is the estimation project from the Udacity Flying car nanodegree program. Below are the details on how the estimator was implemented.

### 1: Sensor Noise ###

Based on the sensor log data (in the `config/log` folder), the standard deviation for the GPS and IMU acceleration data was calculated. The calculation were done in a jupyter notebook: [standard deviation](standard_deviation.ipynb)

### 2: Attitude Estimation ###
The attitue estimation was performed based on the IMU data and implemented in the function `UpdateFromIMU`. The Euler angles were converted to quaternions using the `FromEuler123_RPY` function and then integrated (using the quaternions `IntegrateBodyRate` function and the IMU update rate (dtIMU)) to get the pitch, roll and yaw. Finally, the yaw value was normalized to be within the -pi - pi range.

### 3: Prediction ###
The implementation of the Extended Kalman Filter (EKF) prediction state was based on the information provided in the [Estimation for Quadrotors paper] (Estimation_for_Quadrotors.pdf), specifically in section 7.2. The implementation was done in the `Predict` `PredictState`, `GetRbgPrime` functions. 

The `PredictState` function converts the acceleration from the body frame to the inertial frame and integrate those values to calculate the velocities increments which is added to the previous velocities in the state vector. The gravity constant is taking into consideration when integrating the acceleration in the Z axis. For the position calculations, the velocities are integrated to calculate the position increments which is added to the previous position in the state vector.

The `GetRbgPrime` function calculates the derivative of the rotation matrix (Rgb) which rotates from the body frame to the global frame. RbgPrime is defined in Equation 52 in the [Estimation for Quadrotors paper] (Estimation_for_Quadrotors.pdf)

The `Predict` funtion preditcs the state forward and update the covariance varibale (ekfCov) based on the Jacobian (gPrime) as shown in equation 51 in the [Estimation for Quadrotors paper] (Estimation_for_Quadrotors.pdf). The ekfCov is calculated based on the equations presented in the Algorithm 2 table in the [Estimation for Quadrotors paper] (Estimation_for_Quadrotors.pdf).

### 4: Magnetometer Update ###
The readings from the magnetometer are used to update yaw. For that, the Jacobian (hPrime) is defined according to equation 58 in the [Estimation for Quadrotors paper] (Estimation_for_Quadrotors.pdf). Then the yaw value was normalized to be within the -pi - pi range.

### 5: Closed Loop + GPS Update ###
The GPS data was used to estimate the positions and velocities. The `UpdateFromGPS` function calculate the Jacobioan (hPrime) for the GPS update based on equation 55 in section 7.3.1 in the [Estimation for Quadrotors paper] (Estimation_for_Quadrotors.pdf).

### 6: Adding Your Controller ###
The controller developed on the Control project was incorporated within this project and the `QuadControlParams.txt` parameters were re-tuned.
