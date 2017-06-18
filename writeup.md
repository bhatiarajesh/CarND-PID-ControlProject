# Overview
-----

This repository contains all the code needed to complete the PID controller project in Udacity's Self-Driving Car Nanodegree. A proportional–integral–derivative controller (PID controller) is a control loop feedback mechanism (controller).  A PID controller continuously calculates an crosstalk error value CTE as the difference between a desired setpoint and a measured process variable and applies a correction based on proportional, integral, and derivative terms (sometimes denoted P, I, and D respectively) which give their name to the controller type. 

# Hyperparameter tuning/optimization
-----

The base algorithm of the PID controller implemented in the project is based on what was introduced in the PID controller lesson. However, the hyper parameters were tuned manually to determine the effect based on the different PID components. The twiddle and gradient descent approaches were attempted but I didn't get the parameters to converge to any useful values. 

# Effect of PID components
-----

In PID, 
P accounts for present values of the error. For example, if the error is large and positive, the control output will also be large and positive.
I accounts for past values of the error. For example, if the current output is not sufficiently strong, the integral of the error will accumulate over time, and the controller will respond by applying a stronger action.
D accounts for possible future trends of the error, based on its current rate of change. The D module reduces the strength of the action to prevent an overshot.

The 'P' component allows the car to get close to the CTE=0 line but there is an oscillation around it. It makes the car react to the CTE value in a linear fashion.For example with a (large) value of 1.0 as seen here we observe that the control output is large (25 degree) causing an overshoot.

[![SDC - PID controller p=1 ](https://github.com/bhatiarajesh/CarND-PID-ControlProject/raw/master/out/pid-controller-HIGH-kp.png)]

This is less dramatic for smaller values of 'Kp', but whenever the car encounters a corner, the CTE increases faster and this induces a violent steering correction which often results in the car going off track. With Kp(i.e. 0.001) of a smaller value, the car doesn't react fast enough, the oscillations around the center line seem to have a very low frequency. Considering the above into account, 0.1 seems to be a good start value for Kp.

The 'D' component dampens(as expected) the oscillations induced by the 'P' component. A low value for Kd (0.1) seems to not control the oscillations induced by ‘P’ much as can be seen below. 
[![SDC - PID controller d=0.1 ](https://github.com/bhatiarajesh/CarND-PID-ControlProject/raw/master/out/pid-controller-LOW-kd.png)]

A value of 1.0 for the Kd seems to control the oscillations better and return to the desired setpoint quicker and smoother.

The 'I' component is meant to adjust for bias in the control, for example when the alignment of the car is incorrect so the wheels aren't pointing straight when the steering input is zero. There seems to be no bias in the project, which means no value of 'Ki' is required. 

# Changing throttle
-----

When changing throttle from 0.3 to 1.0, I noticed that the car was not able to pass the track successfully and there were large oscillations as shows here.

[![SDC - PID controller d=0.1 ](https://github.com/bhatiarajesh/CarND-PID-ControlProject/raw/master/out/pid-controller-HIGH-throttle.png)]


# Simulating bias
-----

When adding a constant value (ex. 0.3) to the input steering angle (steering_value) we could simulate a bias. I observed that the bias caused the car to overshoot but when setting Ki to 0.001, this bias is corrected(after some initial oscillation), so that the car is also able to pass the track

# Conclusion
-----

Based on manually tuning the hyper parameters, I settled on the values for Kp = 0.1 , Ki = 0.001 and Kd = 1.0 as good tuning values to make the car circulate around the whole track successfully.

[![SDC - PID controller p=0.1 and d=1.0, i=0.001 ](https://github.com/bhatiarajesh/CarND-PID-ControlProject/raw/master/out/pid-controller-success.gif)]





