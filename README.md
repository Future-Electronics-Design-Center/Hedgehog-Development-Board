# Hedgehog blinky_soc Reference Design

### Follow this link to the [blinky_soc GitHub repository](https://github.com/Future-Electronics-Design-Center/hedgehog-blinky_soc/tree/main)

## Overview

- This is a "basic blinky", out-of-box reference design for the Hedgehog Development Board
- The design implements a soft RISC-V MC MCU running 90MHz
- The design also supports UART output via the FTDI FT2232HL port B micro USB connector J3
- This design currently targets Lattice Propel/Radiant v2023.2
- Refer to the [Propel Design Summary](soc_html/SoC_Memory_Map.html) for details


**Follow the sections below, in order, to build and run the design on the Hedgehog board**

## 1. Prerequsites

The following Propel Builder IPs need to be installed prior to running the tcl script in the next step.  **Be sure to have these pre-installed first.**

| Propel IP | Version |
| --------- | ------ |
| RISC-V MC | v2.5.0 |
| GPIO	| v1.6.2 |
| I2C Controller | v2.0.1 |
| SPI Controller | v2.1.0 |
| System Memory	| v2.1.0 |
| UART | v1.3.0 |
| AHB Lite Interconnect | v1.3.0 |
| AHB Lite to APB Bridge | v1.1.0 |
| APB Interconnect | v1.2.0 |


## 2. Getting Started - Propel Builder

There is a tcl script (blinky_soc_gen.tcl) in the propel folder that can be used to automatically generate the Propel Builder project for this reference design.  Startup Propel Builder v2023.2 and in the Tcl Console, first use the `% cd <path>` command to change directory to the location of the blinky_soc_gen.tcl file and then enter the command `% source blinky_soc_gen.tcl`.  After the Tcl script completes, right-click in the schematic window to re-draw and cleanup the layout.  The Propel Builder project output is automatically generated as the last step in the script.

If your Lattice install folder for Propel Builder is not in the default location, it may be necessary to edit the blinky_soc_gen.tcl script to update the location.  Open the script in your favorite editor and modify the following line that sets the install path for Lattice Propel. `set propelRegenLsccBase "C:/lscc/propel/2023.2"`

Propel Builder generates a Tcl script within the path of the Propel Builder project (in this case `propel/blinky_soc/radiant_setup_template.tcl`) that in turn will create a Lattice Radiant project to build the HDL design and ultimately generate an FPGA bitstream.  This Tcl script is automatically sourced in Radiant when clicking the "Run Radiant" button (or from the Design menu item) in Propel Builder.

## 3. Getting Started - Radiant

With the Propel design opened in Radiant, the next step would be to add the constraint files to the project.  There is a pre-synthesis constraint file (hedgehog.sdc) that specifies the input clock frequency as 125MHz and a post-synthesis constraint file that sets the IO and pin assignment constaints for the Hedgehog board. These files are found in the constraints folder of the repository.  Use the File->Add->Existing File... menu option with the appropriate file type filter selected (.sdc/.pdc) to add both constraint files to the project.  Click on the "Export Files" process button to run through synthesis, map, place & route and bitstream file generation.

## 4. Getting started - Propel SDK

Startup Lattice Propel v2023.2 to build the software design.  Propel will first ask for the location of your workspace.  The exact location isn't critical and can be created wherever you prefer.  For convenience, the folder workspace/ is included in the .gitignore for this repository so you can create a workspace folder within the folder of this reference design repository.  Once the workspace has been created, the software project needs to be "imported" into the workspace.  From the File menu, select the "Import..." item.  From the import dialog, expand the "General" folder and select "Existing Projects into Workspace". Make sure the "Select root directory:" options is selected and then click the "Browse..." button to the blinky_sw folder.  The blinky_soc project should be detected and selected in the Projects window.  Click the "Finish" button to complete the import.  Click on the "Build" button to generate the Debug software.

## 5. Configuring the FPGA

Use the Lattice Programmer (accessible from Radiant) to program the FPGA via the J3 micro-USB connector.  Use the default "Fast Configuration" programming operation to write the bitstream directly to the FPGA configuration SRAM.  See the section [Storing Bitstream to Flash]() for directions on storing the configuration image to the internal configuration flash.

## 6. Debugging/Running the RISC-V Software

From within Lattice Propel, click on the "Debug" button in the toolbar.  The first time this is done for a new project, a Debug Configurations dialog will appear.  Double-click on the "GDB OpenOCD Debug" item in the pane on the left to configure the debugger.  A new debug configuration is automatically created called "blinky_soc Debug" with much of the configuration details populated.  It is likely that you will be required to setup the "CableConn" tab of the configuration by clicking on the Detect Cable button and then clicking on the Scan Device button.  If the Scan Device button doesn't show the LCMXO5-25 device, it may be necessary to select a different "Port:" selection from the ports discovered when clicking the Detect Cable button.  Click the Apply button to save the updates to the CableConn tab and then click on the Debug button at the bottom of the dialog.  It can take some time (10-15 seconds) for the debugger to startup.  Be patient waiting for it to initialize and load the program into memory.  The program should startup in a paused state with an automatic breakpoint set at the first line of code in main().  Click the Resume button to continue operation.  When running the code monitors the four DIP switch positions (SW2) and provides updates via UART when the switch states change.