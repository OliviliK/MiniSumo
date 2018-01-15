# STM32CubeMx Import to EmBitz 1.11

The basic assumption is that the reader has __EmBitz 1.11__ and __STMCubeMX__ installed.  It is possible that a newer version of EmBitz will reduce the required effort.  There is no particular requirement for STM32CubeMx version. The testing of these steps is done with versions 4.19.0, 4.22.1, and 4.23.0.

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

----

## Basic Steps

1. Create a new EmBitz Project
1. Remove files from Project Tree
1. Delete all files except **[ProjectName].ebp**
1. Create a new STM32CubeMX Project in EmBitz Project Folder
1. Generate STM32CubeMX code
1. In EmBitz Project add Files to Project Tree,remove duplicates, and update Defintions

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
* Delete all subfolders
* Delete all other files except **[ProjectName].ebp**

----

## Start a New STM32CubeMX Project
### Create a New Project
* Open STM32CubeMX
* Select New Project
* Select Series, such as STM32F4
* Select Lines, such as STM32F407/417
* Select Part No, such as STM407VETx
* Click OK (or Start Project) to confirm the selection

### Enable Debugging Pins
* In Pinout Configuration Peripherals, select __SYS__
* In Debug line, select __Serial Wire__
  * This enables the __ST-LINK V2__ connection to the 4 __SWD__ pins

### Define Project Settings

* Select: Project, Settings ... (Alt-P)
* Enter Project Name as [ProjFolder], this is the same as the EmBitz project
* Select Project Location as [ProjParentFolder]
  * This allows STM32CubeMX to create folders and files for EmBitz without copy operations 
* Select Other Toolchains (GPDSC)
* Open Code Generator tab
* Select "Copy only the necessary library files"
* Click OK to confirm the settings
* It is possible that some firmware files need to be updated, accept the downloads
* Select: File, Save Project (Ctrl-S)

### Generate Code
* Select: Project, Generate Code (Ctrl-Shift-G)
* Click "Open Folder" to see the generated folders and files
* Observe how
  * The **[ProjectName].ebp** file did stay
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
* Click OK to iclude both Debug and Release
* In project tree select: Sources, CMSIS, Device, ST, [device series], Source, Templates
* In the Templates, under __arm__, __gcc__, and __iar__, there are startup assembly files (extension small s) and all of theme have to be removed
  * In some newer versions of STM32CubeMX, the extra assembly files are not created
* Right click the file name and select "Remove file from project" for these 3 files

### Add startup and loader files
* Locate the STM32Cube repository on your disk (created by STM32CubeMx installation)
  * A typical location is **C:\Users\[user name]\STM32Cube\Repository**
* Select the latest version of the target MCU, such as **STM32Cube_FW_F4_V1.18.0**
* Select suitable platform template, such as **Projects\STM32F4-Discovery\Templates**
* Select the startup file in **SW4STM32** folder
  * Such as **C:\Users\[user name]\STM32Cube\Repository\STM32Cube_FW_F4_V1.18.0\Projects\STM32F4-Discovery\Templates\SW4STM32\startup_stm32f407xx.s**
* Copy the startup assembly file into **[ProjectFolder]\Src**
* Copy the best matching loader file into **[ProjectFolder]** from
  * C:\Users\[user name]\STM32Cube\Repository\STM32Cube_FW_F4_V1.18.0\Projects\STM32F4-Discovery\Templates\SW4STM32\STM32F4-Discovery\STM32F407VGTx_FLASH.ld
* In this case there was no F407VE file and the F407VG was the closest match
* Optionally update the memory size in the copied loader file
* In EmBitz add the assembly file into project
* Select: Project, Build Options ... (Alt-F7)
* Select [ProjFolder] on top of __Debug__ and __Release__ to cover both build type target
* Select __Device tab__
* In __Linker script__ field enter **./[loader file].ld**, such as ./STM32F407VGTx_FLASH.ld

### Update Symbol Definitions
* Select: Project, Build Options ... (Alt-F7)
* Select [ProjFolder] on top of __Debug__ and __Release__ to cover both build type target
* In Compiler settings, in __#defines__ tab
* Remove all default symbols
* Add the device type, such as __STM32F407xx__
* If you don't know the device type, you can leave this field empty and find out the device type based on the compiler error message.  The source code shows the expected device types and you can choose the matching one.

### Build All
* Select: Buid, Rebuild all targets
* Observe that there were 0 errors and 0 warnings
* If there are error messages, the fix is obvious
  * Add the device type into #defines
  * Remove source and header files from the project belonging to IAR, RVDS, or other IDE tools
  * Leave the GCC and SW4STM32 files
* Here are some example directories
  * Middlewares\Third_Party\FreeRTOS\Source\portable\IAR\ARM_CM4F
  * Middlewares\Third_Party\FreeRTOS\Source\portable\RVDS\ARM_CM4F
* Some of the conflicts are not detected during compilation, but during linking beore loading
* Here are some example files
  * Drivers\CMSIS\Device\ST\STM32F4xx\Source\Templates\system_stm32F4xx.c
* The extra files should not be deleted because the next code genereation by CubeMx will add them back

----

## Start Debugger
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

----

# Add SVD File for Symbolic Names of Peripheral Registers
* Use google to find **Resource Selector System View Description**
* Open the www.st.com › Home › Resources page
* Click on the series name, such as STM32F4
* Open the downloaded ZIP file
* Move the SVD file into [ProjFolder]
* In EmBitz, select Debug, Interfaces, select the downloaded SVD file
