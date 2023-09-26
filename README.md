# Training September-October 2023

## Hardware

The aim of this section is locating and explaining main hardware elements in the telescope.

Main hardware elements:

- OSS
  - Location
  - Work:
    - Oil supply (bearings and brake oil supply)
    - Distributed cabinet temperature control (we provide just the setpoint)
  - How does it works from EUI, and what it can and can't do
- Pier cabinet
  - Location
  - Explain the elements that are wired here
    - All the fixed elements below AZ (not rotating)
- ACW
  - Location
  - How it works and what it can do.
    - HW Limits and location
    - How to get out of the HW limits
    - What to check if the position of the ACW is very wrong for some reason
      - Fix: power on AZ, do home, and the system will fail when ACW will try to go to the "wrong" position
- Capacitor bank cabinets (Not Tekniker, limited knowledge)
  - Location
  - Capacitor 8 Cabinets
  - Charging cabinet
  - Power distribution cabinets
  - Indicators
- Azimuth drives
  - Explain location and how they work (we provide torque setpoint through ethercat)
  - Temperature control
- Azimuth limit switches and topple block
  - Location and how they work
- Azimuth encoder tape and heads
  - Explain location and how they work
  - Wired to EIB
- Elevation drives
  - Explain location and how they work
  - Temperature control
- Elevation encoder tape and heads
  - Explain location and how they work
  - Wired to EIB
- Elevation limit switches
  - Location and how they work (all range and operational AZ doesn't have this)
- Main azimuth cabinet (AZ-0001)
  - Location
  - Temperature control
  - Contents:
    - PXIs
    - Bosch controller and drives (ACW and CCW), the rest are distributed in the motors
    - Safety controller, totally independent from the MCS control, main target **PROTECT PEOPLE** (equipment damage is converted to people damage)
    - Ethercat devices, two lines:
      - I/Os
      - Drives
    - EIB (there is just 1 for both AZ and EL)
    - RMC
    - Ethernet connections
- Azimuth cabinet 0101
  - Location
  - Temperature control
  - Contents:
    - IOs
    - Safety IOs
- Main power supply (Not Tekniker, limited knowledge)
  - Location
  - Temperature control
  - How does it works from EUI, and what it can and can't do

  ```plantuml
  @startuml
  agent acLine
  agent powerSupply
  agent capacitorBank
  agent AZdrives
  agent ELdrives
  interface "<U+0000>" as 1
  interface "<U+0000>" as 2
  interface "<U+0000>" as 3

  acLine <-> powerSupply
  powerSupply <-> 1: DC bus
  1 <-d-> capacitorBank
  1 <-> 2: DC bus
  2 <-d-> AZdrives
  2 <-> 3: DC bus
  3 <-d-> ELdrives
  @enduml
  ```

- Locking pins
  - Location
  - Use
  - Limits, which indicate position in the EUI but are also wired to the safety system
- Deployable platforms
  - Location
  - Use
  - Limits, which indicate position in the EUI but are also wired to the safety system
  - How to request permission to extend platforms from the EUI, as the locks are managed by the Safety PLC (PILZ)
- Mirror cover (with locks)
  - Location
  - Use
  - Limits, which indicate position in the EUI
  - Manual disable switch for maintenance
- Elevation cabinets inside CST
  - Location
  - Contents:
    - IOs
    - Safety IOs
- Balancing
  - Location
  - Use
  - Limits
- Auxiliary boxes (temp control)(Not Tekniker, limited knowledge)
  - Location
  - Temperature control
- TEC if ready
  - Location
- CCW if ready
  - Location
  - Use
  - Limits, deviation and range limits

## Software

### Configuration tools

- EIB configuration tool
  - There is some documentation on how to change the IP [here](https://gitlab.tekniker.es/publico/3151-lsst/documentation/maintenancedocuments/eib/eib_changeip)
  useful as a start point.
- Bosch configuration tool [folder with 3 docs:](https://gitlab.tekniker.es/publico/3151-lsst/documentation/maintenancedocuments/boschcontroller)
  - **Bosch_Controller_Startup_Configuration**: This repo contains the document for the startup of the Bosch Controller (MLC) and its configuration.
  - **BoschRexrothRecovery**: This repo has the documentation to recover the Bosch Rexroth hardware from an stacked situation or a major fault.
  - **ReplaceBoschMotor**: Documentation with procedure to replace a Bosch motor and configure it. In this document there are no mechanical instructions for the replacement.
- TwinCAT, for [ethercat diagnosis](https://gitlab.tekniker.es/publico/3151-lsst/documentation/maintenancedocuments/ethercat/ethercatlinediagnostic)
- NI distributed system manager: manages the ethercat from the PXIs, this can only run on windows
- EZ-Zone Configurator: for the temperature controller in the AZ-0001 cabinet. Backup config file
  [here](https://gitlab.tekniker.es/aut/projects/3151-LSST/harwareconfigurations/watlowconfiguration)
- Startup+: for the ethercat modules in all 5 cabinets, AZ-0001, AZ-0101, EL-0101, EL-0102 and PI-0001. This configuration
  must be done locally using a micro usb cable. The backup configuration files are saved
  [here](https://gitlab.tekniker.es/aut/projects/3151-LSST/LabVIEWCode/PXIController/-/tree/develop/ESIFiles/Phoenix/IOConfiguration?ref_type=heads)

### PAS4000

This is the tool used to code the PILZ safety controllers used in the telescope.

#### Safety matrix

The code that must be included in the safety controller is defined in the safety matrix. Here the actions for the detected
causes are listed. This way, when a cause occurs, the system knows how to react to it in a safe manner.

#### Using the tool

This tool is used as a PLC development environment here the best is to familiarize with it with a training from PILZ
directly. The easiest things to do here are force variables, this can be done for example to override the brakes.

### Configuration files

There are multiple configuration files in the TMA.

#### EUI config files

> **Modifying these files could affect the performance of the system**

The EUI has the following config files:

- `HMIConfig.xml`: general config for the EUI
- `HMIWindowsTelemetryVariables.ini`: variables for each window in the EUI
- `TelemetryTopicsConfiguration.ini`: variables for each topic sent to the CSC
- `HMI_UserManagementFile.uat`: user config file, encrypted

#### PXI config files

> **Modifying these files could affect the performance of the system**

Each PXI has a different set of config files, as each PXI runs a different executable. Config files per target:

- [TMA PXI](https://gitlab.tekniker.es/publico/3151-lsst/documentation/pxicontroller_documentation/-/blob/develop/80%20DeployOnTargets/01%20TMA%20PXI.md?ref_type=heads#configuration-files)
- [AUX PXI](https://gitlab.tekniker.es/publico/3151-lsst/documentation/pxicontroller_documentation/-/blob/develop/80%20DeployOnTargets/02%20AUX%20PXI.md?ref_type=heads#configuration-files)
- [AXES PXI](https://gitlab.tekniker.es/publico/3151-lsst/documentation/pxicontroller_documentation/-/blob/develop/80%20DeployOnTargets/03%20AXES%20PXI.md?ref_type=heads#configuration-files)

These files can be modified to change the behavior of the system, for example some systems requiere and update if the
target IP changes, for example the OSS IP is defined in the AUX PXI inside the `/c/OSS/ServerConfig.ini` file. It is
also important to keep the names of the files, if changed the corresponding task may not start, this could be a way for
disabling certain subsystem if necessary.

### EUI

- Review the EUI
- Explain relevant settings for maintenance purposes
  - Locking pins disable elevation angle check
  - Disable modbus temperature controllers when cabinets disabled
  - Elevation parking
  - Mirror cover stuck, disable collision checks, dangerous
  - Force ambient temperature in the EUI
- Setting sets
- Users
- Alarms

#### Settings

TODO: Disable Auxiliary cabinets when disconnecting the Camera or any cabinet with temperature controller

## Error diagnosis

Here is very important to have a general knowledge of the telescope.

Most common errors related with hardware are:

- EtherCAT line failure, here it can be just some minor issue which can be easily recovered by
  [**this**](https://gitlab.tekniker.es/publico/3151-lsst/documentation/maintenancedocuments/ethercat/manageethercatlinestatus)
  procedure or a greater issue, that can not be recovered with the mentioned procedure and
  [**this**](https://gitlab.tekniker.es/publico/3151-lsst/documentation/maintenancedocuments/ethercat/ethercatlinediagnostic)
  other procedure is required
- Bosch controller failure, [**procedure**](https://gitlab.tekniker.es/publico/3151-lsst/documentation/maintenancedocuments/boschcontroller/boschrexrothrecovery)

There are other faults that can occur that are not actually due to hardware problems, and that can be recovered easily,
just knowledge an understanding about the issue is required. Some of these could be:

- Pressing a limit switch
- Moving too fast causing the system to fail due to over-speed
- Temperature alarms, if the system is too far above the setpoint
- Temperature warnings, if the system is too far below the setpoint
- Pressing an ETPB (emergency trip push button), which causes the system to stop
- Having safety interlocks that prevent the movement or activation of a subsystem. For example: powering on elevation
  with the locking pins at the inserted position.

### Log files

#### Location

TODO:

#### Explained

TODO:

## Other options

TODO: Otros mover AZ con ACW???
