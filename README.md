# Pico_Executions

This project aims to explain how to get started with Raspberry Pi Pico on Windows (using the C/C++ SDK and GDB with a Pico Probe) and other RP2040-based boards. It is inspired by, and is roughly equivalent to, the pico-setup project for Linux systems. I learned most of this through a [Digikey tutorial](https://www.digikey.com/en/maker/projects/raspberry-pi-pico-and-rp2040-cc-part-1-blink-and-vs-code/7102fb8bca95452e9df6150f39ae8422) and the [detailed websites that follow](https://shawnhymel.com/2096/how-to-set-up-raspberry-pi-pico-c-c-toolchain-on-windows-with-vs-code/), but also many online forums / messing around myself.

However, Shawn Hymel's Tutorial doesn't fully explain how to get GDB / OpenOCD working to debug the Pico. it also doesn't incorporate the new Pico Debug Probe, which I used and will explain how to setup.

# General Software Setup

Begin by downloading [Microsoft Visual Studio Code](https://code.visualstudio.com/) as it offers many tools (compiling, debugging, and extensions).

Now create a folder named "VSARM" in the top level of your C drive.
In C:\VSARM, create the following folders:

C:\VSARM\armcc
C:\VSARM\lib
C:\VSARM\mingw
C:\VSARM\sdk

If you use other ARM compilers, they can coexist in VSARM.

Now download the GNU C compiler (GCC) and run the installation, but be sure to install into **C:\VSARM\armcc**

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/be5f24a8-f0f8-49dc-964d-376a9549e699)

Continue with the installation process. When it is complete, select the option to **Add path to environment variable.**

![image](https://github.com/Ezoorp/Pico_Executions/assets/112518361/8b39d444-5396-4e86-bdcb-a078f8c5185d)
