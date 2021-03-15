# Marlin_MakerSelectv2
Configuration files for Marlin 2.0.x for a Monoprice Maker Select v2.1.

# Introduction
These configurations are intended for users of a Monoprice Maker Select v2.1 with a BLTouch installed. This configuration keeps LCD and SD card support intact on the stock Melzi board, while adding automatic bilinear bed leveling with a BLTouch.

For BLTouch installation instructions, there is an excellent guide [here](http://blog.osdev.org/3d/printing/2019/10/13/wanhao-duplicator-i3-bltouch.html). My configuration is based off of the ones linked in the blog post, albeit with slight modifications to make it more usable and add support for Marlin 2.0.

I highly recommend reading through both config files before flashing your firmware, so you can understand what changes you may need or want to make.

# Compilation
Initial compiles of the board may require you to set some compiler flags to save ROM space. I recommend compiling with PlatformIO, as you may run into path length issues with the standard Arduino IDE. If you do run into path issues, you may be able to resolve them by moving your Marlin directory up a few levels to shorten the path length.

Here are the compiler flags I used. With these compiler flags on Marlin 2.0.7.2 (latest release at the time of writing), the firmware takes up about 97.1% of the flash. Without these flags, it may not fit in the flash on the ATMega used in the stock Melzi board.

    -fmerge-all-constants -fno-tree-scev-cprop -fno-split-wide-types -ffast-math -finline-limit=3 -mcall-prologues
    
# Changes Dependent on Your Printer
There are several changes you may need to make on your printer before it prints properly.

The main thing to pay attention to is your BLTouch mount. I recommend [this mount](https://www.thingiverse.com/thing:2719441) as it works with the Dii3 cooler installed.

Once your BLTouch is mounted and installed, it is extremely important to set the X and Y offsets on line 982. Leave the Z offset to zero and adjust it later with the LCD.

After you have flashed the firmware, run PID tuning on your extruder and bed.

You can do this by running the GCODE

    M303 E0 S200 C8
    
and

    M303 E1 S60 C8
    
respectively.

Once you have obtained the Kp, Ki, and Kd values, edit the PID section in Configuration.h and recompile and reflash.

# Final Steps

Once you have reflashed your firmware with Marlin, there are a few GCODE commands to keep in mind.

    G28 ;Home all axes. You can specify X, Y, Z, or any combination to only home certain axes.
    G29 ;Levels the bed. Must be run AFTER G28. Only saves bed level data to RAM unless you run M500!!
    M112 ;Emergency stop. Shuts off all steppers and heaters.
    M500 ;Saves configuration to EEPROM. Should be done after each bed level or after each Z-offset change.
    M501 ;Load settings from EEPROM. Run when you want to use pre-saved bed level data instead of running G29 again.
