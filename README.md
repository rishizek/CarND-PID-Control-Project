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

There's an experimental patch for windows in this [PR](https://github.com/udacity/CarND-PID-Control-Project/pull/3)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid Kp Ki Kd`. *Note parameters Kp, Ki, and Kd are added to argument to make parameter-tuning easier. 

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

[video1]: ./output.mp4

## Reflection
### Describe the effect each of the P, I, D components had in your implementation.
#### P
As we increase the Kp (the coefficient for the proportional term) value, the car moves more rapidly to the center of the road, as P controller explains the present value of the cross track error. If, however, we use too large Kp, the car oscillates faster and moves off the road quickly.

#### I
The I controller represents the cumulated value of the past cross track error, and this controller is useful to remove the systematic bias of the car. As we increase the Ki (the coefficient for the integral term), the correction of the systematic bias becomes stronger. However, if we increase the value too much, the vehicle do not converge to the center of the road, because I conroller dominates PD controllers in such case.

#### D
The D controller is useful for the gradual reduction of oscillation (damping effect). 
The increase of the Kd (the coefficient for the derivative term) lets the vehicle approach to the center of the road more smoothly but less quickly. Therefore, if we set value too large, the car may not react quick enough when the road is curved.

#### Result
Here's a [link to my video result][video1]
with Kp = 0.15, Ki = 0.001, and Kd = 1.0.


### Describe how the final hyperparameters were chosen.

I tuned the hyperparameters manually as the implementation of the optimiser for this model is quite complex and manual tuning provides decent result for my case. I describe my approach below.

Initially I only turned Kp and Kd values assuming the simulator does not have the systematic bias (namely Ki = 0.0).
Next I set Kp = 1.0 and Kd = 0.0 (with Ki = 0.0) with twiddle method in mind. Then, the car rapidly oscillated and quickly went out of the road. Therefore, I decreased the Kp and also increased Kd values so that the car moves toward the center of the road more gradually and converge there. With I set Kp = 0.15 and Kd = 1.0 (with Ki = 0.0), the car stabilized around the center of road, and was able to run the complete track without touching the edge of road. 

After that, to stabilized the vehicle further, I turned Ki value with keeping the above Kp and Kd values. There, I first tried Ki = 1.0 but the car immediately diverged the course as presumably I controller dominates other controllers. After several try and error, I found Ki = 0.001 worked best for my case. It is probably because the simulator has very small systematic bias.

Here's my final parameters for the model:
Kp = 0.15, Ki = 0.001, and Kd = 1.0.