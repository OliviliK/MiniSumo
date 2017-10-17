# STM32CubeMx Import to EmBitz 1.11

The basic assumption is that the reader has __EmBitz 1.11__ and __STMCubeMX__ installed.  It is possible that a newer version of EmBitz will reduce the required effort.  There is no particular requirement for STM32CubeMx version. The testing of these steps is done with versions 4.19.0 and 4.22.1.

The testing did confirm that the STM32CubeMX migration from 4.19.0 to 4.22.1 is working with EmBitz.

----
## Current State
The [EmBitz](https://www.embitz.org/) IDE uses the classic Standard Peripheral Library from ST for ARM processors.  ST has stopped the development of SPL and created [STM32CubeMX](http://www.st.com/content/st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-configurators-and-code-generators/stm32cubemx.html) as a modern alternative.

### EmBitz
This subject has been discussed in EmBitz [Forums](https://www.embitz.org/forum/) for several years and there are expectations that a new version after 1.11 could have a native support for importing from STM32CubeMx.

This article is inspired by a [post](https://www.embitz.org/forum/thread-710-page-2.html) made by user **Traubensaft** with a reference to a German youtube posting.

### STM32CubeMx
For me, a significant benefit is a support for **F7** processors that are not covered by SPL.  Like EmBitz, the STM32CubeMx is free. 

> STM32Cube includes STM32CubeMX, a graphical software configuration tool that allows the generation of C initialization code using graphical wizards.

> It also embeds a comprehensive software platform, delivered per Series (such as STM32CubeF4 for STM32F4 Series). This platform includes the STM32Cube HAL (an STM32 abstraction layer embedded software ensuring maximized portability across the STM32 portfolio), the STM32Cube LL (low-layer APIs, a fast, light-weight, expert-oriented layer), plus a consistent set of middleware components such as RTOS, USB, TCP/IP and graphics. All the embedded software utilities are delivered with a full set of examples.

### Application Libraries
In addition to the platform and RTOS library, third-party libraries are used in the EmBitz ARM projects. For example, the fast **BLDC ESC** communication protocol **DShot** is described in [BetaFlight GITHUB Wiki](https://github.com/betaflight/betaflight/wiki/DSHOT-ESC-Protocol) and the code can be found in [pwm 
output.c](https://github.com/betaflight/betaflight/blob/master/src/main/drivers/pwm_output.c) file.

The BetaFlight application library uses STM32CubeMX HAL and LL API. 

----
## Basic Steps

1. Create a new EmBitz Project
1. Remove files from Project Tree
1. Delete all files except **startup_stm32f4xx.S**
1. Create a new STM32CubeMX Project in EmBitz Project Folder
1. Generate STM32CubeMX code
1. In EmBitz Project add Files to Project Tree and update Defintions

----
## Start an Empty EmBitz Project
### Create a New Project
* Open EmBitz
* Select: File, New, Project ...
* Select STmicro-ARM
* Enter Project title, such as [ProjFolder]
* Select Project folder, such as [ProjParentFolder]
* Leave the compiler default values unchanged
* Select Device Family, such as Cortex M4
* Select Device Series, such as STM32F4xx
* Select processor, such as STM32F407VE
* Unselect Standard Peripheral Library
* Leave the debugger default values unchanged

### Remove Project Files
* Select Management pane
* In Projects tab, right click the new project
* Select Remove files ...
* Click OK to remove all files
* Click Yes to confirm the removals
* Observe that the project tree is empty
* Select: File, Save everything (or Alt-Shift-S)
* Close EmBitz

### Clean Project Folders and Files
* Open the project folder
* Observe the subfolders cmis, inc, and src
* Delete __cmis__ and __inc__ subfolders
  * cmis has some arm and core files
  * inc has stm32 and system header files
* Open __src__ subfolder
* Delete all other files except __startup_stm32f4xx.S__
  * The deleted files are main and system C-files

----
## Start a New STM32CubeMX Project
### Create a New Project
* Open STM32CubeMX
* Select New Project
* Select Series, such as STM32F4
* Select Lines, such as STM32F407/417
* Select MCU, such as STM407VETx
* Click OK to confirm the selection

### Enable Debugging Pins
* In Pinout Configuration Peripherals, select __SYS__
* In Debug line, select __Serial Wire__
  * This enables the __ST-LINK V2__ connection to the 4 __SWD__ pins

### Define Project Settings

* Select: Project, Settings ... (Alt-P)
* Enter Project Name as [ProjFolder]
* Select Project Location as [ProjParentFolder]
  * This allows STM32CubeMX to create folders and files for EmBitz without copy operations 
* Select Other Toolchains (GPDSC)
* Open Code Generator tab
* Select "Copy only the necessary library files"
* Click OK to confirm the settings
* Select: File, Save Project (Ctrl-S)

### Generate Code
* Select: Project, Generate Code (Ctrl-Shift-G)
* Click "Open Folder" to see the generated folders and files
* Observe how
  * The **startup_stm32f4xx.S** assembly file did stay in src folder
  * Folders __Drivers__ and __Inc__ were created
  * Files __.mxproject__, __[ProjFolder].gpdsc__, and __[ProjFolder].ioc__ were created
* Exit STM32CubeMX

----
## Update EmBitz Project
### Remove the Startup Files for the Other IDE Tools
* Select Management pane
* In Projects tab, select the new project
* Right click the project name
* Select "Add files recursively ..."
* Click OK to see the additions
* Click OK to include all files
* In project tree select: ASM Sources, CMSIS, Device, ST, [device series], Source, Templates
* In the Templates, under __arm__, __gcc__, and __iar__, there are startup assembly files (extension small s) and all of theme have to be removed
* Right click the file name and select "Remove file from project" for these 3 files

### Update Symbol Definitions
* Select: Project, Build Options ... (Alt-F7)
* Select [ProjFolder] on top of __Debug__ and __Release__ to cover both build type target
* In Compiler settings, in __#define__ tab
* Add the device type with three numbers, such as __STM32F407xx__.  The EmBitz default types, such as *STM32F407VE* and *STM32F4XX*, are not used by STM32CubeMX code
* Remove symbol *__FPU_USED* to avoid duplicate definitions
* Remove unused symbols, such as *STM32F407VE* and *STM32F4XX*

### Build All
* Select: Buid, Build all targets
* Observe that there are no error messages

### Start Debugger
* Select: Debug, Start Debug Session (F8)
* Observe that the loading and the executions start are working                 

----
## Add the Other Parts of Your Project
* Open STM32CubeMX
* Add and modify your project
* Generate code
* Open EmBitz
* Update your project tree
* Build and debug
