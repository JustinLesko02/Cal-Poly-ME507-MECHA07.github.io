# ME507 MECHA07 TERM PROJECT # {#mainpage}

## Hello! Welcome to our Mechatronics Term Project.  Created by Evan Long and Justin Lesko.

The following content documents our completion of Cal Poly's ME507 Mechatronics Term Project.  The overall goal of the
 project was to select a personal project that requires automation and design a custom PCB to accomplish the task.
 Our project is an automatic cat feeder.  The user will place a large quantity of dry cat food in the storage container
 of the feeder, and the feeder will automatically dispense the food at the appropriate time for the cat.  Over the course
 of the spring 2025 quarter, we designed the cat feeder and custom PCB, ordered the parts, wired everything up, and 
 programmed the feeder to create a functional final product.

The cat feeder dispenses food with a large lead screw, and has a pair of load cells to measure the weight of food in the
 bowl.  The feeder is equiped with a wifi module and is programmed to act as an http server.  With port forwarding set up
 on a local network, the user can use any browser to access the feeder controls from anywhere in with internet access.
 As a result, the user can be out of town and still log in to feed the cat.

The goal of this website is to document our efforts complete the project.  On this page, we include an overview of our 
 mechanical, electrical, and software designs.  Also included on this page is navigation to more in depth documentation
 of the rest of our software.

Figure XX.  Image of the cat feeder.

Figure XX.  Video of the cat feeder dispensing food

Figure XX.  Screenshot of the cat feeder website.


### Mechanical Design

The mechanical structure was constructed primarily with aluminum and acryllic sheets that are tabbed and slotted together.
 The primary dispensing action is accomplished with a 3D printed lead screw powered by a geared stepper motor.  To assist
 the filling, there is a second stepper motor with a brush flap to push the cat food all the way into the bowl.


Figure XX.  Isometric view of the cat feeder 3D model.

Figure XX.  Isometric view of the cat feeder lead screw.


### Electrical Design

All the electronic circuitry is designed into the custom PCB.  The PCB accepts 12V power from an external power supply,
 and provides terminal block interfaces to connect 2 stepper motors, 2 encoders, and 2 load cells.  The wifi module is
 connected directly to the PCB via female headers.  The PCB contains a power regulation circuit, a STM32F411 
 microcontroller (MCU), stepper motor drivers, an ADC IC, and a calendar IC.  

The PCB was fabricated and assembled by an online PCB manufacturing service.  The board and power supply are installed 
 underneath the feeder, and the motors and load cells are wired directly the board with the requirement that no wires can be 
 exposed on the outside of the feeder, to prevent the cat from chewing the cables.  The board was programmed with an ST-Link
 module connected via pin headers.

Figure XX.  Image of the assembled PCB.

Figure XX.  Image of the installed PCB and power supply.

Figure XX.  Schematic of the microcontroller circuitry.

Figure XX.  Schematic of the power supply circuitry.

Figure XX.  Schematic of the ADC circuitry.

Figure XX.  Screenshot of the PCB design.


### Software Design

The software installed on the MCU has two goals.  The first goal is dispensing the correct amount of food into
 the bowl when required.  The second goal is hosting an http server to accept user inputs.  The software is written in c and
 is divided into two classes: Filler and Interface, which handle the filling and the server, respectively.

A link to our project github can be found here: 

Naturally, the 96MHz MCU speed is overkill for the speed that the cat feeder needs to update its outputs.  As
 a result, we have the freedom to complete the control logic with simple Finite State Machines (FSM).  The filling action
 is accompished with two FSMs.  The first FSM is responsible for reading the load cell values from the ADC.  The second 
 FSM is responsible for updating the stepper motor speeds at the appropriate time.

The largest challenge of the software was getting the http server working.  The server is entirely embedded within the
 MCU and requires no external computing.  When a browser attempts to connect to the IP address of the wifi module, the wifi 
 module forwards the request over UART to the MCU.  The MCU stores the request in a buffer, then searches the buffer for
 keywords.  If it recognizes the request, it sends a simple http website back to the wifi module which in turn gets forwarded
 to the browser.

The main function first initializes the interface, then the ADC, and finally starts the main loop.  The main loop then calls
 the run_interface function, which checks for UART commands from the ST-Link and connection requests from the wifi module and
 responds with the appropriate data.  The run_interface function is non-blocking.  Then the main loop calls the run_filler 
 function, which runs the ADC FSM and the filling FSM.  The main loop is slowed to a speed of 100Hz to allow time for the wifi
 module uart connection to fill in the buffer.

Figure XX.  State space diagram of the main FSM.

Figure XX.  State space diagram of the wifi initialization FSM

Figure XX.  State space diagram of the ADC reading FSM

Figure XX.  State space diagram of the filling FSM

