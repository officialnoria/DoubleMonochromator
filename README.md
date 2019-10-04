
# DoubleMonochromator
Only still images, code is property of Centro Nacional de Metrologia
This is the final version of the software I developed for Centro Nacional de Metrologia (CENAM). This image corresponds to when the homing routine has concluded.
Image
The software is used to control:
* Two grating motors
* Three openings motors
* One filter wheel motor
* A three-axis motor system
* Laboratory equipment (LockIn Amplifier, Multiplexer, Digital Multimeter)

I am not allowed to show the source code or laboratory setups, but here's a description of the most important methods used.

**A method to move the grating motors**

    moveGratingMotors(lambda)

It receives the destination wavelength, and moves the two grating motors to produce the desired color at the output. This method makes use of a mathematical model to map lambda to motor steps, the model was determined with reverse engineering by me.

**A method to open/close each opening**

    moveOpenings(opening, distance)

The system has three independent openings labeled Entrance, Intermediate, Exit. This method receives the opening ID string and the desired distance to open/close, and moves the desired opening to that distance

**A method to move the filter wheel**

    moveFilters(lambda)

Similar to the `moveGratingMotors(lambda)` method, this one moves a motor to a previously determined position to filter the light at the output. Unlike the grating motors, this is a discrete function, not continuous.

**A method to perform a spectral scan**

    moveInSequence()

In the UI, the user can select a starting lambda, an end lambda, and a step size, and select the green EJECUTAR button. When this happens, the system moves to the starting lambda, pauses for 5 seconds, then moves to the next lambda, moving as many nanometers as the step size number is, until it reaches the stop lambda, or the closest lambda not greater than end lambda.

**A method to move each axis individually, and a method to move the three axis simultaneously**

    moveAxis(index, position)
    moveThreeAxis(position)

Both methods are very similar, the difference is the instruction sent to the controller, in the case of the individual axis, you just pass the selected axis and the distance i.e. G0X100, where the index variable determines which axis to move (1=X, 2=Y, 3=Z), whereas in the three axis movement method you pass the full command G0X100Y300Z1000, for example.

**A method to fix the positions of the reference cells**

    fixPosition(channel)

The main purpose of this system is to characterize solar reference cells, up to four per measurement, so the user needs to position each cell in a platform, and this platform is the one that moves in three dimensions with the previous two methods. Once the user has positioned a cell, it needs to save the XYZ position so, in the measurement, the system can move to that position and measure that cell. This method captures the current XYZ position and stores it for future use.

**The method that executes the measurement**

    performTest()

To successfully perform this method, the user must have selected and positioned at least one cell, selected the range, number and integration time of the measurements with the multimeter, the wavelength range, and the number of repetitions the test must perform. The algorithm is protected by CENAM.

**A method for saving measurements data**

This method saves the measured data to a txt formatted for post-processing with Microsoft Excel.

Two mathematical models were determined for this software:

**A model that maps lambda to steps in the gratings motor**

This is a polynomial model which was determined using the original software for the monochromator. To determine the validity, a spectrometer was used to measure the error. The maximum error is 2 nm.

**A model that maps lambda to opening distance**

Due to metrological reasons, the bandwidth of the output must be either 5 or 10 nm, this is achieved by varying the opening distance and measuring the bandwidth with a spectrometer. Then a model is developed so that the openings move according to the current lambda to ensure the desired bandwidth.
