# MiniSumo
Project to build a mini sumo robot using new technology.  Mini sumo rules restrict the weight to 500 g, length to 10 cm, and width to 10 cm.

----

## Technology

### Battery

LiPo 3S, 11.1V, 1500 mAh, 40C, Using deans connector.

### Speed Controller

Four channel RockStar RS20Ax4 Electronic Speed Controller for BLDC motors. Using deans connector for power and 4 3-point Amass MR30 Plugs for motor connections. The MCU connections are with dupont connectors using dshot protocol.

### Wheel Rims

Cut bands from Michelin A1 Airstop 700 x 25-32c 40mm Bike Tube.  The friction of the tube inside is better than the outside.  The tubes are streched directly over the motor cans.

### Motors

The BLDC motors, Brushless 2208 80T 90 kV, are normally used in camera gimbal applications.  The low kV value allows these to be used in sumo robot without a gearbox.

### Sensors

Pololu 8 channel analog QTR reflectance sensor is used to detect the dohyo boarders.  It can be used also for line tracing applications.

Fourteen Time-of-Flight Ranging Sensors, VL53L0X, are used to detect the opponent. Eight are use for coarse observation with 360 coverage.  Six are used for higher resolution in forward view.

Bosch Inertial Measurment Unit, bno055, is used to aid the movements of the robot.

### Controllers

The main controller is STM32F407VE Micro Controller Unit.  The sub controller is STM32F103C8 to manage the ToF sensor arrays.

### Mechanics

The mechanical parts are printed using ABS filaments.  The main component contains the motors batteries, power management, ESC, and main controller.  The cover component contains the ToF sensors and the sub controller.

The parts were designed with AutoDesk Fusion 360, sliced with Simplify3D, and printed with MakerGear M3.

### Software

Development was done with EmBitz Integfrated Development Environment.  The pin level design was done with STM SMT32CubeMx tool.  Additional development was done to support the seamless integration between EmBitz and STM32CubeMx.  The native EmBitz supports only Standard Peripheral Library.  This project uses the Harware Abstraction Library from STM instead of the SPL.

### Debugging

The two controllers are connected to EmBitz with ST-LINK V2.

----

## Applications

The motivation for this project was to do a "better" zumo-robot.  In that sense the applications are similar what Pololu has developed for the Zumo 32U4 robot.
