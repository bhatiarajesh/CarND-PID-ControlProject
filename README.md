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


——————————————————————————————————————————————




# CarND-Controls-PID
Self-Driving Car Engineer Nanodegree Program

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `./install-mac.sh` or `./install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.
* Simulator. You can download these from the [project intro page](https://github.com/udacity/self-driving-car-sim/releases) in the classroom.

There's an experimental patch for windows in this [PR](https://github.com/udacity/CarND-PID-ControlProject/pull/3)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`. 

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/e8235395-22dd-4b87-88e0-d108c5e5bbf4/concepts/6a4d8d42-6a04-4aa6-b284-1697c0fd6562)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./
