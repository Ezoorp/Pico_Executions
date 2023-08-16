## Pico Executions Project

This project explains how to get started with Raspberry Pi Pico on Windows (using the C/C++ SDK and GDB with a Pico Probe) and other RP2040-based boards. 
It is roughly equivalent to, the pico-setup project for Linux systems and a [Digikey tutorial](https://www.digikey.com/en/maker/projects/raspberry-pi-pico-and-rp2040-cc-part-1-blink-and-vs-code/7102fb8bca95452e9df6150f39ae8422) - Windows version -> [detailed websites that follow](https://shawnhymel.com/2096/how-to-set-up-raspberry-pi-pico-c-c-toolchain-on-windows-with-vs-code/).

However, Shawn Hymel's Tutorial doesn't fully explain how to get GDB / OpenOCD working with a Pico debug probe, and there aren't many explanations online.
**The second half of this tutorial is how to setup a Pico debug probe on Windows**

I am also leaving below a couple really awesome resources for the Pico:
[Datasheet / Pinout](https://datasheets.raspberrypi.com/pico/pico-datasheet.pdf)

[APIs / SDK / General Programming](https://www.raspberrypi.com/documentation/pico-sdk/)

[Tutorials / Guide to Pico](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf)



## General Compiling Setup

Begin by downloading [Microsoft Visual Studio Code](https://code.visualstudio.com/) as it offers many tools (compiling, debugging, and extensions). I also recommend downloading the C,Cmake,Cmake tools, and python extensions.

Now create a folder named "VSARM" in the top level of your C drive.
In C:\VSARM, create the following folders:

C:\VSARM\armcc
C:\VSARM\lib
C:\VSARM\mingw
C:\VSARM\sdk

If you use other ARM compilers, they can coexist in VSARM.

# Compiler Setup

Now download the GNU C compiler (GCC) and run the installation, but be sure to install into **C:\VSARM\armcc.** For my projects I use GCC 10.3.1

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/be5f24a8-f0f8-49dc-964d-376a9549e699)

Continue with the installation process. When it is complete, select the option to **Add path to environment variable.**

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/8b39d444-5396-4e86-bdcb-a078f8c5185d)

Although we might be able to stop here, it helps to be able to compile and run your code onto both **embedded and Windows systems.** It is often easier to develop code on your computer than to get the embedded system to work.

So we will download [MinGW](https://sourceforge.net/projects/mingw-w64/files/) (Minimalist GNU for Windows):

Download the i686-posix-sjlj zip file for your desired MinGW-W64 GCC version (e.g. 8.1.0). 

Use 7-Zip to unzip it into the C:\VSARM\mingw directory. Uncheck the named unzip directory so that when everything unzips, you should have C:\VSARM\mingw\mingw32.

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/3263508d-3c56-40d3-b8c8-f7905b65f59a)

When it’s done, open a Windows Command Prompt and enter the following into the terminal:

`echo mingw32-make %* > C:\VSARM\mingw\mingw32\bin\make.bat`

This creates a wrapper batch file that will call the mingw32-make tool whenever you type make into a Windows terminal. We will update the Windows Path to find all of the tools in mingw32\bin (along with this .bat file) in a later step.

# Cmake Setup
Although Cmake is not a compiler, it will automate many steps of compilation to make our lives easier during programming. The Raspberry Pi SDK uses Cmake to help create build files.

Head to the [download page on CMake’s site.](https://cmake.org/download/) I use version 3.26.4. I attempted this with 3.27.2 and it [TODO: WHETHER IT WORKED]
Download the windows installer (cmake-3.27.2-windows-x86_64.msi)

Run the installer and accept the user license. On Install Options, **select Add CMake to the system PATH for all users.**

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/d27be66a-099c-4165-b797-771b6bced950)

Note that this will install CMake to C:\Program Files\CMake, which is fine, as it will be used as a system-wide tool (not just for VSARM projects).

# Python Setup

The Pico SDK relies on Python to script and automate some of the build functions.

At the time of writing, the Pico SDK recommends Python version 3.9. I have made this work with Python 3.9, and attempted with 3.11.3 and it [TODO: WHETHER IT WORKED]

Head to the [downloads page on the Python site.](https://www.python.org/downloads/)
Install launcher for all users (recommended) is checked and check Add Python 3.9 to PATH.

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/ec10f781-677e-4e7b-9485-a788cdf43c2e)

At the end of the installation process, select the option to disable the MAX_PATH length limit.

If you were not asked to disable the MAX_PATH length limit, you will want to make sure that long paths are enabled. The Pico SDK (and many other SDKs) often have long, nested folders, resulting in pathnames that exceed the original Windows limit (260 characters).



To enable long paths, search for "regedit" in the Windows search bar and run the Registry Editor program. Navigate to `Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem` and add an entry (if one is not already there) for `LongPathsEnabled`. Change Value data to 1 and Base to Hexadecimal. 

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/186064fb-8ea0-41b8-8443-2ab0fab24192)

# Install Git
Git makes downloading the SDK and other peoples' code much easier. We can change the shell in Vs Code to Git Bash so we are essentially operating on a Linux.

Download [Git here](https://git-scm.com/download/win)

When you get to the screen asking you to choose a default editor, feel free to pick whatever you want. I chose emacs because I know it (barely) enough to edit git comments.

# Setup Pico SDK
The Pico SDK contains tools and libraries developed for the Pico / RP2040. Whenever we call the SDK for libraries, we will call this folder:
create a folder named pico in C:\VSARM\sdk

We could download and unzip the SDK into here, but lets use git for ease of use.

Open Git Bash and enter the following commands.

```
cd /c/VSARM/sdk/pico 
git clone -b master https://github.com/raspberrypi/pico-sdk.git 
cd pico-sdk 
git submodule update --init 
cd .. 
git clone -b master https://github.com/raspberrypi/pico-examples.git
```
![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/118ee1c4-86c6-496f-8b74-2c2a46dcd144)

Note that this will cause you to compile the entire SDK everytime you compile your code.

At this point, you should have all of the necessary build tools, SDK, and examples installed to start developing programs for the Raspberry Pi Pico and RP2040.

# Update Env Variables
We still want to setup some variables in path to make calling them easy and programming quick.

In the Windows search bar, enter env. Click on Edit the system environment variables.

In that window, click on Environment Variables…

Under User variables for <username>, select Path and click Edit.

Add C:\VSARM\mingw\mingw32\bin as a new entry. This will allow us to use things like gcc and ld to build C and C++ programs for Windows.

Make sure you see the following entries listed:

C:\VSARM\armcc\<release version>\bin
C:\VSARM\mingw\mingw32\bin
You might see an entry for Python if you chose to install Python for the current user (as I did).

Under User variables for <username>, click New… and add the following entry:

Variable name: PICO_SDK_PATH
Variable value: C:\VSARM\sdk\pico\pico-sdk

At this point your environment variables should include some new path variables and a new user variable:

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/316d2218-b6d6-4ac5-9178-521e52cd5e1a)

Under System variables, select Path and click Edit. Check to make sure you see the following entries (add them if you do not see them):

C:\Program Files\CMake\bin
C:\Program Files\Git\cmd

Click OK on all 3 of the open windows to close them and save changes.

At this point, you should be able to open a commend prompt and enter commands like `gcc, make, and echo %PICO_SDK_PATH%` to make sure the environment variables were set correctly.

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/f1cd2c11-d616-4c31-8bc1-e58eb6ef25f0)

# Test Compiler / Build Env
Now that we have everything setup for building code, lets test it. Open VS Code and click Terminal > new terminal. The default window is likely a windows powershell, but we want a Git Bash, so click the drop down and select new git bash

<img width="673" alt="image" src="https://github.com/Ezoorp/Pico_Executions/assets/112518361/54650130-8286-4659-814c-7c0617e18085">

Just like we did with the make.bat file (for Windows terminals), I recommend creating an alias for mingw32-make.exe in Git Bash. Enter the following commands (you will only need to do this once):

```
echo "alias make=mingw32-make.exe" >> ~/.bashrc
source ~/.bashrc
```

Now we will build the blink example and make our Pico blink!

```
cd /c/VSARM/sdk/pico/pico-examples/
mkdir build
cd build
cmake -G "MinGW Makefiles" ..
cd blink
make
```

** Common Error: ** If it returns "Cmake Error at pico_sdk_import.cmake Please set SDK PATH" implies just that. If you hadn't set the env variables, go do that. Otherwise close and reopen VS Code or Reboot. Usually VS Code doesn't catch that you changed the variables unless it is turned on and off.

** Note: ** Any time you call CMake from a terminal like this, you will need to specify “MinGW Makefiles” as the build system generator (-G option).

** Note: ** The Pico-W is slightly different from the Pico. For blink and similar examples, compile the examples within the pico-w/wifi/ like blink. More on how to compile for WiFi in a later project.

Now you have generated makefiles for the entire SDK and built blink. We can easily upload the .uf2 file to our Pico without any special tools.

Put the Pico board into bootloader mode (press and hold the BOOTSEL button while plugging a USB cable into the Pico).

Find which drive letter the RPI-RP2 drive is mounted to (e.g. it was G: for me). Enter the following into Git Bash (change the drive letter as necessary):
`cp blink.uf2 /g/`


# TODO - Pico W Blink: 
let's build the blink example for Pico-W

```
cd /c/VSARM/sdk/pico/pico-examples/
mkdir build
cd build
cd pico_w
cmake -G "MinGW Makefiles" ..
cd ..
make
```
Problem - Didnt compile.

# Automating Compile/Build Process with Cmake / VSCode
Rather than deal with compiling / linking the entire code base everytime we change the code, we can use some tools like Cmake to help us. 

Note that compiling the examples from before are different from actually compiling a project from scratch due to the cmake structure used in those examples. I have added a blink folder with an example where we will practice building from within VS Code
Make sure you downloaded the C, Cmake, Cmake tools extensions on VS Code.

* insert shawn hymel stuff *



## GNU Debugging Setup
Debugging can be done with print statements, but at some point being able to break and see registers or run through operations helps a lot. However, such a tool is quite difficult to setup due to the complexity of the system required to debug.

We will use GDB (GNU DeBugger) as it is a fairly common debugger for GNU based systems and we already downloaded it with the GCC downloads earlier. However we need a port on our computer to read data from the JTAG port on the Raspberry Pi. So let's get OpenOCD (On Chip Debugger) to do that.

# Setting up OpenOCD with Pico Debug Probe
As of now, OpenOCD can work on windows, but it had problems before with the RP2040. Some people advocate for building OpenOCD yourself, which solved some issues on my previous projects, but to get you up and running, we will attempt using a pre-built OpenOCD. I tried (Shawn Hymel's IpenOCD tutorial)[https://shawnhymel.com/2168/how-to-build-openocd-and-picotool-for-the-raspberry-pi-pico-on-windows/], but his windows version of OpenOCD could not open the debug access port to communicate with the probe. Despite taking it apart and identifying the bug, it wouldn't work.

But RPi has their own OpenOCD on their's website and it worked, so we are going with that.

Downloaded OpenOCD zip off the (rpi pico website)[[https://www.raspberrypi.com/documentation/microcontrollers/debug-probe.html](https://github.com/raspberrypi/pico-setup-windows/releases/latest/download/openocd-x64-standalone.zip)] and unzip. We will call the openocd.exe from VSCode or the command line, so its important we put this somewhere safe and relevant like **C:/VSARM/openocd** or if you have another build of OpenOCD and only want this one for the Pico **C:/VSARM/sdk/pico/openocd**

[**Insert photo here of the openocd in the folder on my comp**]

# VS Code Setup

Now download the Cortex Debug extension on VsCode.

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/aab699ac-3c81-4190-9c1b-55432f2082bf)

We need to give the extension some relevant information about the debug port, the debugger, and the target system. Create a folder wherever you are writing your code called `.vscode` - do not do this in your pico examples code, that code has cmake setup for all the examples which doesnt work very well for this.

In that folder make a file called launch.json and paste the following code:
```

{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Pico Debug",
            "cwd": "${workspaceRoot}",
            "executable": "${command:cmake.launchTargetPath}",
            "request": "launch",
            "type": "cortex-debug",
            "servertype": "external",
            // This may need to be arm-none-eabi-gdb depending on your system
            "gdbPath" : "arm-none-eabi-gdb",
            "gdbTarget": "localhost:3333",
            //"device": "RP2040",
            "configFiles": [
                "interface/cmsis-dap.cfg",
                "target/rp2040.cfg"
            ],

            "svdFile": "${env:PICO_SDK_PATH}/src/rp2040/hardware_regs/rp2040.svd",
            "runToEntryPoint": "main",
            // Work around for stopping at main on restart
            "postRestartCommands": [
                "break main",
                "continue"
            ],
            "searchDir": ["C:/VSARM/sdk/pico/openocd/scripts"],
            "showDevDebugOutput": "raw"
        }
    ]
}

```
The most important parameters are:
1. searchDir should be the path to your openocd scripts (not the .exe) so OpenOCD can find your hardware configuration
2. interface/cmsis-dap.cfg - this is the hardware configuration used to inteface with a debugger. Usually this is picoprobe, but we change it to cmsis-dap
3. gdbTarget is usually RP2040 or picoprobe, but we changed it to localhost:3333 for the probe

Now we need to create a settings.json file to point to the openocd.exe

```
{
    // These settings tweaks to the cmake plugin will ensure
    // that you debug using cortex-debug instead of trying to launch
    // a Pico binary on the host
    "cmake.statusbar.advanced": {
        "debug": {
            "visibility": "hidden"
        },
        "launch": {
            "visibility": "hidden"
        },
        "build": {
            "visibility": "default"
        },
        "buildTarget": {
            "visibility": "hidden"
        }
    },
    "cmake.buildBeforeRun": true,
    "C_Cpp.default.configurationProvider": "ms-vscode.cmake-tools",
    "cortex-debug.openocdPath": "<PATH TO OPENOCD.EXE>"
}
```

make sure you include the path to the openocd.exe as shown above.

Common error - using zip file instead of real openocd folder

# Hardware Setup
Now that the software is setup, lets try setting up the hardware. First plug in the pico probe to your computer and make sure it displays a red light to indicate it is on.
Then plug the DEBUG connector on the probe to the SWD on the bottom of the picom (opposite the microusb port). Below shows how to wire it up if you don't have a connector:

[TODO: diagram for plug in a connector]

Now you can leave the microUSB in to continue sending commands through that, or connect the UART pins on the probe to the top three pins on the pico as shown below:

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/ec4e303b-ce06-48be-95ee-745055d9720b)

I usually just use the microusb, but haven't experimented with this much.

Now click the run and debug button on the left side bar of VS Code and there should be a green play button next to "pico Debug" in the top bar. This will be how you debug the pico. Feel free to set a break point or just run until main.

This is the point at which bugs pop up, so pay attention to 
1. The Output will tell you if it successfully compiled, linked, built the code you were planning to use
2. The Terminal will tell you if OpenOCD succeeded in connecting to the Probe, the Pico, and uploading the code.

If successful, a bar should pop up in the top with buttons to click through and debug the code. To check variables, you can look at them on the left bar under variables or you can print into the bottom bar under debug console and it will return variables you want to print.


# How to run GDB command line (old school)
Sometimes VSCode is being problematic and it helps to be able to run GDB from a command line. To do this, open git bash and we will basically run all the previous things, but from a command line:
```
cd <enter path to OpenOCD.exe here> ....
./openocd.exe -s ../tcl -f interface/cmsis-dap.cfg -f target/rp2040.cfg -c "adapter speed 5000"

```
When this works it should return "Listening on port 3333" or "Using CMSIS-DAPv2 interface"

<img width="454" alt="image" src="https://github.com/Ezoorp/Pico_Executions/assets/112518361/fa1bf835-96b7-4e72-9714-f8d763a0443e">


Now we move to VSCode where we open a git bash terminal.
**Build/cmake/make your code**
```
arm-none-eabi-gdb.exe {insert built program here.elf}
target remote localhost:3333
*insert breakpoints as you would in gdb*
load
monitor reset init
*begin debugging/passing through breakpoints*
```
<img width="462" alt="image" src="https://github.com/Ezoorp/Pico_Executions/assets/112518361/4c334759-8867-4451-a70e-58175e904fe7">

<img width="657" alt="image" src="https://github.com/Ezoorp/Pico_Executions/assets/112518361/21db59ea-b48d-47d4-9798-81b470bf93af">


**Note:** if you add breakpoints to the script, those breakpoints require reloading the program. Sometimes I would run and not see my breakpoints, but since this is embedded GDB we need to upload any change to the chip itself.


Note the changes that need to be made to cmakelists for debug probe rather than picoprobe

