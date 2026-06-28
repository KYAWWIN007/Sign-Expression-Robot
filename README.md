# Sign-Expression-Robot
Sign Expression Robot
To view or edit the full mechanical assembly model of the robotic arm:

Navigate to the CAD File/ directory in this repository.
Download all compressed archive volumes (3D CAD File_Part 1.zip through 3D CAD File_Part 5.zip).
Extract all zip volumes into a single, shared directory on your local machine.
Open your 3D CAD modeling software solidworks.
Locate and open the primary top-level assembly file named right arm assembly (or its corresponding extension file). This will load the fully mated, multi-joint linkage structure including the hand, forearm, and geared shoulder stages.

Actuator to Driver Connections
Macro-Joint Stepper Motors (Shoulder Yaw & Pitch): Connect the 4-wire pairs (A+, A-, B+, B-) of your NEMA 23 stepper motors directly to the corresponding terminal outputs on their dedicated TB6600 drivers.
End-Effector Servos (5 Fingers & 1 Wrist): Plug the 3-pin servo connectors directly into the PCA9685 16-Channel PWM Driver Block starting from channels 0 through 6:
Channel 0: JX PDI-6225MG Elbow Flexion Servo
Channel 1: MG996R Wrist Rotation Servo Channel \
Channel 2: MG90S Thumb Servo
Channel 3: MG90S Index Finger Servo
Channel 4: MG90S Middle Finger Servo
Channel 5: MG90S Ring Finger Servo
Channel 6: MG90S Little Finger Servo
Control Logic Interconnections
PCA9685 I2C Bus: Route the logic control lines from the PCA9685 board to the Arduino Mega 2560:
SDA pin ──► Pin 20 (SDA) on Arduino Mega
SCL pin ──► Pin 21 (SCL) on Arduino Mega
VCC pin ──► 5V on Arduino Mega (Logic Power)
GND pin ──► GND on Arduino Mega
TB6600 Driver Logic Pins: Route the pulse (step) and direction signals from the Arduino Mega to the optocoupler logic inputs on the TB6600 drivers:
Shoulder Yaw Driver: PUL+ ──► Pin 3 | DIR+ ──► Pin 4
Shoulder Pitch Driver: PUL+ ──► Pin 5 | DIR+ ──► Pin 6
Note: Tie all PUL- and DIR- terminals from both drivers together and connect them to a common GND pin on the Arduino Mega.
Isolated Power Rail Configurations
    CRITICAL SAFETY NOTE: Keep the high-current inductive motor loops completely isolated from the processing logic to prevent transient   current spikes from freezing or destroying your Arduino. Do not bridge the positive terminals of your power supplies.
   24V DC Rail: Wire this high-voltage power supply directly to the VCC and GND inputs of the TB6600 stepper drivers to provide enough current for the NEMA 23 coils.
6V DC Rail: Wire this independent supply directly to the high-current screw terminal block (power input) on the PCA9685 board to cleanly power the 6 servos.
5V DC Rail: Use a standard USB Type-B cable connected to your host PC (or a dedicated 5V logic supply) to power the Arduino Mega 2560.
Common Ground Bridge: Connect the negative terminal (GND) of the 24V supply, the 6V supply, and one of the GND pins on the Arduino Mega together at a single point (star ground). This ensures all control signals share a clean, stable reference voltage.


WARNING: ABSENCE OF HARDWARE LIMIT SWITCHES & EMERGENCY STOP (E-STOP) CIRCUITS
READ BEFORE POWERING THE ACTUATION SUB-SYSTEMS
This prototype configuration operates under strict open-loop positioning constraints for the macro-joint assembly. Users must carefully review the following system limitations before applying power to the motor rails:

No Physical Limit Switches: The NEMA 23 shoulder yaw and pitch mechanisms do not feature physical limit switches, proximity sensors, or hard end-stop cutouts. The microcontroller tracks positioning solely via step-count variables in the software. If the stepper motors receive an incorrect directional command or excessive step parameters, they will continuously drive the arm past its physical boundaries. This can cause immediate mechanical binding, structural fracturing of the 3D-printed brackets, and permanent gear damage within the EBA-23 planetary gearboxes.

No Dedicated Hardware Emergency Stop (E-Stop): This circuit topology lacks a dedicated, hardware-isolated physical E-Stop button. Cutting off logic or communication commands via the serial interface will not instantly halt the high-torque inductive 24V DC power line if the drivers are locked in a transient pulse loop.
