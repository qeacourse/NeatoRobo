---
title: "Meeto your Neato!"
toc_sticky: true
---

## Purpose of this How-to

This document will help you to get you up and running with your Neato robot.  After following these instructions you will be able to connect to your robot, examine its sensor data, and drive it around.


## Overview

In this module we will be programming the Neato BotVac. The Neato is a powerful, low cost robot platform that we have customized for QEA (it's also technically a vacuum cleaner, but we'll be ignoring that for this module!). We have engineered the platform to abstract away a lot of the frustrating bits, allowing you to focus on learning the really fun robotics, physics, math, and computing content.

<p align="center">
<img src="Pictures/neato_overview.jpeg" alt="A picture of a Neato robotic vacuum cleaner with a custom remote control interface based on Raspberry Pi" width="60%" height="60%">
</p>

The robots have each been outfitted with a Raspberry Pi. The Raspberry Pi is a low cost, Linux computer that will serve as a bridge between your laptop and the robot. To use the robot you will initiate a connection from your laptop (in MATLAB), via the Olin network, to the Raspberry Pi.  Once the connection has been made, the Raspberry Pi will start talking to the robot.  All sensor data will then be streamed from the Raspberry Pi to your laptop over Olin's wireless network. Your laptop will process this sensor data and then send motor commands to the robot over the same network.  In this way, all important computation will be done on your laptop.  This architecture simplifies the sharing of robots and makes debugging and editing code as easy as possible.

Since you are not modifying the code running on the Raspberry Pi, each robot will function identically (i.e. there is no software you need to modify on the robot). However, due to variance in manufacturing and handling over the years, each robot may have its own hardware idiosyncrocies.


## One-Time Setup in MATLAB

You should only have to do the following steps once at the beginning of the semester.

### Step 1: Make sure you have Matlab installed

We used Matlab last semester, but just in case...

### Step 2: Install MATLAB Drive Connector

You can find the download here:

[https://www.mathworks.com/products/matlab-drive.html?s_tid=AO_MLConnector#matlab-drive-connector](https://www.mathworks.com/products/matlab-drive.html?s_tid=AO_MLConnector#matlab-drive-connector)

If prompted for a username and password, use your standard Olin credentials. When you install this program, there will be a local folder in your file system called "MATLAB-Drive." All the files in that folder get synced to the cloud on the MATLAB site, similar to how it is done for Dropbox or Google Drive.

### Step 3: Add Neato files to your MATLAB Drive

Click on the following link:

[https://drive.matlab.com/sharing/97375c34-1e81-461b-854a-28da2c573026/](https://drive.matlab.com/sharing/97375c34-1e81-461b-854a-28da2c573026/)

Then click "Add to my Files" and then "Add Shortcut." Use your Olin credentials for the requested username and password.


### Step 4: Set and save path

On the Home tab of MATLAB, to the right of the Layout button, there is a small icon with multiple folders for "Set Path." Click on that. Then click on "Add with subfolders..." and browse to and select the "MATLAB-Drive" from your files. Click on "Save" to make sure the path is saved. 

### Step 5: Check that the Neato files are in your path

In the Command Window, type 

```matlab
>> which neatov2
```

If you get a location of a file on your laptop, you have set the path correctly. If you get a message saying "'neatov2' not found," repeat the previous step again. Next, quit MATLAB, reopen it, and then repeat this step to make sure you saved the path correctly.


## Connecting to the Physical Neatos 


### Step 1: Grab a USB battery pack for the raspberry Pi

Checklist before performing this step:

1. The battery indicator light should be at least level 2 (preferably full)

### Step 2: Choose your Neato

Checklist before performing this step:

1. Make sure the Neato's batteries are charged.  To test this, pull the Neato away from its charging station and for the newer Neato's hit the button near the front bumper of the Neato that has the home icon on it. For the older Neato's hit the larger orange power button.  The display should illuminate revealing a battery capacity indicator.  Sometimes you will have to click the button below the display to dismiss any errors that show up on the Neato’s screen before the battery level is displayed.

### Step 3: Connect the USB battery pack to the Raspberry Pi's USB cable.

It should take about 1 minute for the robot to be ready to use (see step 4 for a final checklist).

### Step 4: Connecting to the Neato from your Laptop through MATLAB

Checklist before performing this step:

1. Raspberry pi display backlight is illuminated and not flashing on and off (see troubleshooting section for what to do if this is not the case)
2. Raspberry pi display shows that the Neato is connected to the OLIN-ROBOTICS network and has an IP address assigned to it.
3. Raspberry pi display shows that the signal strength of the Neato's connection is at least 70 (the max is 99) for the wifi dongles with antennas, and at least 50 (max is 65) for the dongles without antennas.  Note: If the signal strength is too low, see troubleshooting section for more information.
4. Your laptop is connected to the OLIN-ROBOTICS network (password given in class).

### Step 5: Start up MATLAB on your computer and run the following command:

***Note: replace the part of the command below that says 192.168.16.68 with the IP address of your robot that is shown on the Raspberry Pi's LCD display.***

```matlab
>> neatov2.connect('192.168.16.68');
```

If all went well you should see output like this.
```matlab
Connecting to the Neato.
Testing connection.
Connection successful.
```

## Making the Neatos move

- You can set the left and right wheel velocities with a command like the following one (this sets the wheel velociites in meters per second for the left and right wheel respectively):

```matlab
>> neatov2.setVelocities(.1, -.1);
```

Each wheel velocity has to be between -0.3 and 0.3 meters per second.


The motion of the robot will stop after about half a second, but you can also stop instantaneously by running the following command.

```matlab
>> neatov2.setVelocities(0.0, 0.0);
```


## Viewing the Neatos' sensor measurements

You can visualize the Neato's LIDAR data using the following commands (this assumes you haven't connected to the Neato yet. If you have, remove the neato connect step).

```matlab
>> neatov2.connect('192.168.16.68');
>> s = neatov2.receive();
>> figure;
>> polarplot(s.thetasInRadians, s.ranges, 'b.');
```

Here is an example of what plot might look like.

<p align="center">
<img src="Pictures/lidar_viz.png" alt="A screenshot of the LIDAR data visualization figure" width="60%" height="60%">
</p>

The front of the robot (with the flat side opposite the LIDAR) corresponds to 0 degrees.

More generally, we can fetch the latest sensor data from the Neato using the receive (e.g., ``>> s = neatov2.receive()``).

The data structure ``s`` representing the sensor data will include:   

- bumpers: 0/1 booleans about whether any of the four bumbers are engaged
- thetasInRadians: the LIDAR takes measurements every 1 degree, or 2pi/360 radians, starting at 0. These angles are given in this vector
- ranges: the distance of the first thing (e.g., wall, object) sensed by the LIDAR in the direction of the corresponding angle. For example, the first element of this vector tells the distance to an object directly ahead.

We'll explore the other components soon.

 
## Disconnecting from the Neatos

### Step 1: Run the disconnect command in MATLAB

```matlab
>>> neatov2.disconnect();
```

### Step 2: Shut down the Raspberry Pi

When you are done working with the robot, it is important to properly shutdown the raspberry pi. DO NOT just unplug the battery. To shutdown the pi, push the "up" button once (or more) until you see the message "Press select to Shutdown". Press select and wait for the green "ACT" LED on the left side of the Pi to flash steadily ten times then stay off. It is then safe to unplug the battery.

<p style="text-align: center;">
<img alt="Visual instructions for turning off the Raspberry Pi" src="Pictures/s7vHYkMHZEmtoXhtdLxNdig.png"/>
</p>


### Step 3: Unplug the raspberry pi battery and connect it to a charging station

### Step 4: Return your Neato robot to a charging station

DO NOT try to turn it off through any physical switches on the device. If you do, the battery will not charge for the next group.


## Troubleshooting Your Neato

### *Symptom:* Both the red and green LEDs on the raspberry pi are illuminated and not flashing.

*Potential Cause:* the Pi was unable to boot from its SD card.

<ul>

<li>Solution 1: the first thing to check is that the Raspberry Pi's SD card is fully inserted into the Raspberry Pi.  See the image below for the location of the SD card.  You will know it is fully inserted if you push on the card and it clicks into place.

<p align="center">
<img src="Pictures/raspberry_pi_b_6_0_0.jpg" width="70%"/>
</p>
</li>
<li>Solution 2: if the card is fully inserted, the SD card may have become corrupted (possibly because some people didn't properly shutdown the Raspberry Pi!).  Please send David ([dshuman@olin.edu](dshuman@olin.edu)) an e-mail and tell him which robot is having the problem.  He'll fix it ASAP, but in the meantime just use another robot.</li>
</ul>

###  *Symptom:* the raspberry Pi display's backlight is flashing on and off.

*Potential Cause:* the Pi cannot connect to the robot via the USB cable.

* Solution: sometimes the Neato will turn off due to inactivity.  Press button near the front of the Neato’s bumper labeled with the home icon to wake your Neato up.  If that doesn't work, shutdown and then reboot the Pi.  If none of this works, the robot battery might be dead.  Try recharging the robot.  While the robot is recharging, switch to another robot.


### *Symptom:* the Wifi signal strength indicator on the Raspberry Pi is below 60 even though you are right near an access point.

*Problem:* The Pi has connected to an access point that is not the closest one (this will sometimes happen).

* Solution: Assuming the Pi display is at the screen showing the IP address, press right to enter the network setup menu.  OLIN-ROBOTICS should be highlighted with an asterisk.  Press right again to reconnect the Pi to the Wifi.  If it doesn't work the first time, try one more time.  If it doesn't work then, switch to a new robot.
