## Pico Executions Project

This project explains how to get started with Raspberry Pi Pico on Windows (using the C/C++ SDK and GDB with a Pico Probe) and other RP2040-based boards. 
It is inspired by, and is roughly equivalent to, the pico-setup project for Linux systems and a [Digikey tutorial](https://www.digikey.com/en/maker/projects/raspberry-pi-pico-and-rp2040-cc-part-1-blink-and-vs-code/7102fb8bca95452e9df6150f39ae8422) - Windows version -> [detailed websites that follow](https://shawnhymel.com/2096/how-to-set-up-raspberry-pi-pico-c-c-toolchain-on-windows-with-vs-code/).

However, Shawn Hymel's Tutorial doesn't fully explain how to get GDB / OpenOCD working with a Pico debug probe, and there aren't many explanations online.
** The second half of this tutorial is really where I figured out how to make a debug probe work and the steps you can follow to make it work too**

I am also leaving below a couple really awesome resources for the Pico:
[Datasheet / Pinout)[https://datasheets.raspberrypi.com/pico/pico-datasheet.pdf]
[APIs / SDK / General Programming](https://www.raspberrypi.com/documentation/pico-sdk/)
[Tutorials / Guide to Pico](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf)



## General Compiling Setup

Begin by downloading [Microsoft Visual Studio Code](https://code.visualstudio.com/) as it offers many tools (compiling, debugging, and extensions).

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

## GNU Debugging Setup
Debugging can be done with print statements, but at some point being able to break and see registers or run through operations helps a lot. However, such a tool is quite difficult to setup due to the complexity of the system required to debug.

We will use GDB (GNU DeBugger) as it is a fairly common debugger for GNU based systems. However due to the complexity of the debugger, it requires at least one core. Although the pico has two cores, the 

