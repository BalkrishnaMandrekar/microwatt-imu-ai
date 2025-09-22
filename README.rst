Microwatt Motion AI Controller

=====================================

The Microwatt Motion AI Controller is a user project designed to demonstrate
how to integrate the open-source Microwatt POWER CPU core with the MPU6050 IMU
sensor and a lightweight AI accelerator using the LiteX SoC framework. 
Unlike simple RISC-V + IMU projects, this design highlights the flexibility 
of the OpenPOWER ISA in real-time control applications, with an emphasis 
on extensibility toward AI-driven decision-making.

One reason for choosing the OpenFrame harness is to maximize the usable project
area while retaining full access to GPIOs. This allows the CPU, sensor interface,
and AI engine to be integrated at the same hierarchical level, creating a standalone 
SoC tailored for motion control experimentation.

This project demonstrates:

Integration of the Microwatt POWER CPU core into the OpenFrame harness.

I²C-based communication with the MPU6050 for motion sensing (accelerometer + gyroscope).

A LiteX-connected AI accelerator module for basic inference tasks (e.g., motion classification, adaptive control).

UART/USB debug interfaces for testing and monitoring.

The primary difference between this project and standard OpenFrame
CPU examples is the focus on real-time sensor integration and AI acceleration.
While many OpenFrame examples focus on generic CPU bring-up, 
this project explores a concrete application: mobility and motion control.

Interfaces provided:

I²C interface for the IMU sensor.

UART/USB for debugging and continuous data output.

Wishbone bus for connecting additional LiteX modules.

GPIO access for optional external peripherals.

The motivation is to create a roadmap for future open mobility controllers, 
allowing developers and enthusiasts to experiment with customized 
AI-enabled control systems. While this prototype begins with an FPGA-based design,
the ultimate goal is to demonstrate how such integrations 
can scale into real-time applications in robotics, e-mobility,
and adaptive controllers.

This README provides a high-level overview of the project’s features and motivation. 
Detailed documentation (setup, build, and usage instructions) is provided in the /docs directory.

Table of Contents
-----------------
- Module Overview
- Block diagram 
- Designing
- Building
- Submitting
- Contributing
- License

Module Overview
---------------

The Microwatt Motion AI Controller is built around the Microwatt POWER CPU core.
The SoC integrates motion sensing, control, and AI acceleration through Wishbone-interconnected modules. 
The following IPs are included, with their respective base addresses (example mapping):

RAM: 0x00000000

FLASH: 0x10000000

UART: 0x20000000

GPIO: 0x21000000

I²C Controller (MPU6050): 0x22000000

AI Accelerator (LiteX custom block): 0x23000000

Counter/Timer: 0x24000000

Debug Registers: 0x41000000

The design supports UART/USB debug I/O, sensor data streaming, 
and real-time control loops, with LiteX providing bus interconnection
and peripheral management.

Block diagram
---------
```
<pre>
+------------------------------------+
|                                    |
|            LiteX SoC               |
|               RAM                  |                    +----------------------+
|               UART                 |                    |                      |
|                I2C                 |                    |                      |
|               Timer                |                    |      USB - UART      |
|              AI Block              |     <---------     |    (FTDI on Arty)    |
|   +----------------------------+   |     --------->     |                      |
|   |                            |   |                    |                      |
|   |          Wishbone          |   |                    |    MPU6050 Sensor    |
|   |   +-------------------+    |   |                    |                      |
|   |   |                   |    |   |                    |                      |
|   |   |     Microwatt     |    |   |                    +----------------------+
|   |   |       (CPU)       |    |   |
|   |   |                   |    |   |
|   |   +-------------------+    |   |
|   +----------------------------+   |
+------------------------------------+
</pre>
```

Designing
---------

The main design challenge is integrating the Microwatt CPU with the LiteX-based SoC 
infrastructure and ensuring seamless I²C communication with the MPU6050.The OpenFrame harness exposes full GPIO control, 
so the I²C lines (SCL, SDA) and UART debug pins are managed directly at the SoC level.

Key design features:

Microwatt POWER core as the main CPU.

LiteX SoC builder to integrate peripherals and Wishbone bus interconnect.

I²C IP to communicate with the MPU6050 sensor (accelerometer + gyroscope).

Custom AI accelerator connected via LiteX Wishbone for basic inference tasks (e.g., 
classifying motion states like tilt, balance, or activity).

UART interface for debugging and streaming sensor data.

The hierarchy of the project is:

Top level: user_project_wrapper.v (OpenFrame wrapper).

Second level: microwatt_soc.v (SoC definition with Microwatt core + LiteX modules).

Third level: Peripheral modules (UART, I²C, AI accelerator, timers, GPIO).

Power domains and GPIO handling follow OpenFrame design rules,
with everything mapped into the 1.8V vccd/vssd domains, keeping
the project consistent with the padframe.


Building
--------

Build steps include:

Clone the repository and initialize submodules (Microwatt, LiteX).
Run litex_soc.py to generate the SoC configuration with Microwatt as CPU.
Use FPGA toolchain (Vivado for Arty A7) to synthesize and test on hardware.
Use Renode or Verilator for co-simulation and software verification.

Submitting
----------
This project is intended for demonstration on the Efabless OpenFrame
harness and FPGA prototyping (Arty A7). Submission flow for tapeout 
will follow the Efabless OpenFrame guidelines.

Contributing
------------
Contributions are welcome in the following areas:
Optimizing the LiteX integration with Microwatt.
Improving the AI accelerator (e.g., more advanced inference tasks).
Adding support for more sensors or control algorithms.
Bug fixes and documentation improvements are always encouraged.


License
-------
