# Swerve Components and 

### Main Components
1. Gyroscope
1. Swerve Module
1. Configuration and Subsystem

## 1: Gyroscope

The gyroscope is our point of reference for the robot in real space. We measure x, y, and z coordinates as well as roll, pitch, and yaw respectively.
You can find our current gyroscope here:
- Pigeon 2.0: [link](https://store.ctr-electronics.com/products/pigeon-2?srsltid=AU7gw4XB7m0Wo_mw5sZUADWNUN3F3zSX4iaGkWHZHqQVvNVx23gIDyTm)

We can calibrate it using the following information: (Also there is a cool picture visualizing a space shuttle within the coordinate system!)
- Pigeon Calibration: [link](https://v6.docs.ctr-electronics.com/en/stable/docs/tuner/pigeon-cal.html)

The robot in our case is a square and because we build it from scratch we get to choose what side is the "front". Usually you only have to ask which direction like the robot to be facing during the start of the competition and choose the most optimal side for gyroscope installation.

## Swerve Modules

We can imagine the gyroscope as the center of the robot while providing a set of instructions. We want to have the gyroscope achieve a speed (m/s) in particular direction, and rotate at (deg/s) using either a robot-relative or field relative control scheme. We use the gyroscope as our source of truth for tracking our robot angle at all times. We also use this information in tandem with our Limelight Camera to update our position on the field as we see

Each module has an offset for its position relative to the gyroscope. This is so each module class can do its own set of calculations to know what it needs to do to have the robot move and turn with the instructions provided.

We use a closed-loop control with PIDs and Feed Forward values to get to a desired wheel speed and change the angle of the module quickly and smoothly.

Each module has the following:
- Motor controller and motor for wheel speed
- Motor controller and motor for angle adjustment
- Absolute encoder for precise angle adjustments (Our source of truth for module wheel angle)

The absolute encoder is highly accurate but slow to update quickly. So the optimal way to use it is to use the relative encoder that is a part of the motor that is faster but not as accurate over time and update the relative encoders whenever the wheels are stationary or holding a position long enough to safely update. For a human driver it is not something you have to consider during operation.


## 3: Configuration and Subsystem

Configuration for the gyroscope and measuring the positions of the center of the modules helps give an accurate representation of the system so the wheels will work together and not fight each other. We also have to measure the offsets of the absolute encoder by aligning all the wheels using the same orientation to the front of the robot as each absolute encoder has a sort of compass that has a preset 0 position. No 2 absolute encoders will be facing the same direction with how the encoders are installed in the modules.

You can imagine a subsystem that is set up in the following way:
Joystick input -> translation to x,y, and rotation instructions -> a public Swerve subsystem function that passes instructions to each module

Each module can be considered a part of the subsystem, we pass along the instructions from the function to do some math to calculate the following:
- Desired module wheel angle
- Shortest distance to desired angle either rotating clockwise or counter clockwise
- Desired Wheel Speed

We can choose the shortest distance to rotate even when the motor relative encoder has a positive and negative direction that never changes after initialization.

## In Summary

There are a couple optimizations that are subtle that help it run smoothly but that is the gist of it.

We don't have to build a swerve system from scratch as there are many templates available. We use the following template:
- YAGSL [link](https://yagsl.yassrobotics.com/)

The tricky part is being precise during setup of the gyroscope, the offsets of the absolute encoders, and confirming that the coordinate system of the gyroscope matches the field coordinate system (making complex autonomous possible). After that we need the joystick or game controller inputs to match.

Once we have set this up correctly we should never have to touch it again.... but if we swap a module for another or if the robot changes shape then we will have to make adjustments to confirm it works the same as before.
