# Load_Tunderbolt
Loads safely thunderbolt drivers on 2014 MBP retina with the random shutdown issue

I have a tweaked solution, for who like me needs the thunderbolt ports.

So, moving the AppleThunderboltNHI.kext driver means that every tunderbold device will not be recognised if hot plugged so not only the thunderbolt lan adapter but also hard drives and other things.

If you reboot they will be recognised as PCI devices, but this still results in random shutdowns for me, so it's important to disconnect any tunderbolt device before turning on the machine, also because PCI drivers do not support hot plug, so you can get a kernel panic if you remove e device this way.

So, enter the "hybrid" solution:


First, neutralise the driver by moving it to a different folder without renaming it

if you haven't disabled SIP already 

1) reboot with CMD+R pressed
2) open Terminal
3) sudo csrutil disable
4) reboot

5) open terminal
6) sudo mkdir /System/Library/Extensions_dis (create a directory to store "disabled" drivers)
7) sudo mv /System/Library/Extensions/AppleThunderboltNHI.kext /System/Library/Extensions_dis/AppleThunderboltNHI.kext 

if you want to re-enable SIP
8) reboot with CMD+R pressed
9) csrutil enable
10) reboot 

Now you can create an executable to run every time you need to use the a thunderbolt device, this script will 
load the driver and launch the famous python script to prevent the Mac from crashing.

The python script is using a considerable amount of power so wen the thunderbolt device are not needed its better not to have it running for better battery life and performances.

1) Open automator and create a new "Application" document
2) from the side bar select "Run Shell Script", under Utilities 
3) select the kind of shell as “/usr/bin/python“
4) paste this code:

from time import sleep
import os

os.system ("killall ScriptMonitor") #this avoids having the automator monitor open after running the application

os.system (" echo yourPassword | sudo -S kextload /System/Library/Extensions_dis/AppleThunderboltNHI.kext ") #this loads the driver

#this keeps your mac alive, you can tweak the number, the original one is (0.00002) and corresponds to a quite hight 20% cpu load, (0.001) is around 2% and its seems to be enough on my system
while True:
    sleep(0.001) 

5) replace yourPassword with your system password, it is needed to load the kext

6) export the application, if you prefer you can download it form here https://github.com/pignoniG/Load_Tunderbolt but you need to edith the "Contents/document.wflow" file inside the app and repalce "yourPassword" with your password.



