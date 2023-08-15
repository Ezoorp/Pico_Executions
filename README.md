## Pico Executions Project

This project explains how to get started with Raspberry Pi Pico on Windows (using the C/C++ SDK and GDB with a Pico Probe) and other RP2040-based boards. 
It is inspired by, and is roughly equivalent to, the pico-setup project for Linux systems and a [Digikey tutorial](https://www.digikey.com/en/maker/projects/raspberry-pi-pico-and-rp2040-cc-part-1-blink-and-vs-code/7102fb8bca95452e9df6150f39ae8422) - Windows version -> [detailed websites that follow](https://shawnhymel.com/2096/how-to-set-up-raspberry-pi-pico-c-c-toolchain-on-windows-with-vs-code/).

However, Shawn Hymel's Tutorial doesn't fully explain how to get GDB / OpenOCD working with a Pico debug probe, and there aren't many explanations online.
** The second half of this tutorial is really where I figured out how to make a debug probe work and the steps you can follow to make it work too**

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

`cd /c/VSARM/sdk/pico 
git clone -b master https://github.com/raspberrypi/pico-sdk.git 
cd pico-sdk 
git submodule update --init 
cd .. 
git clone -b master https://github.com/raspberrypi/pico-examples.git
`
