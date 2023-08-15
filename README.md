# Pico_Executions

This project aims to explain how to get started with Raspberry Pi Pico on Windows (using the C/C++ SDK and GDB with a Pico Probe) and other RP2040-based boards. It is inspired by, and is roughly equivalent to, the pico-setup project for Linux systems. I learned most of this through a [Digikey tutorial](https://www.digikey.com/en/maker/projects/raspberry-pi-pico-and-rp2040-cc-part-1-blink-and-vs-code/7102fb8bca95452e9df6150f39ae8422) and the [detailed websites that follow](https://shawnhymel.com/2096/how-to-set-up-raspberry-pi-pico-c-c-toolchain-on-windows-with-vs-code/), but also many online forums / messing around myself.

However, Shawn Hymel's Tutorial doesn't fully explain how to get GDB / OpenOCD working to debug the Pico. it also doesn't incorporate the new Pico Debug Probe, which I used and will explain how to setup.

# General Setup

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

