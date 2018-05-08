# Corolla Emulator
Much of the below is ripped right out of my radar spoofer project. I regret nothing.

I wanted to emulate a Corolla on the Arduino, so I created this project. If you want to play around with OP on an unsupported car, this will also enable your radar and may open the door for some other neato projects. Who knows? "Not I," said the giraffe. "Not I," said the panda.

## HARDWARE REQUIREMENTS:

1. Arduino Mega and CAN shield
	I used a MCP2515 CAN board which I soldered on a 16MHz crystal. 
2. Comma.ai Eon with Openpilot installed
3. Comma. ai Giraffe
	I used a Toyota Giraffe but it should work with a Honda one if you know the pinout
4. Comma.ai Panda
5. Toyota RAV4/Corolla Radar PN: 88210-07010
6: 12VDC power source with a fitting barrel connector for the Giraffe.
7: Potentiometer and 4 buttons
	I got a 4 button ribbon thing from Microcenter

## CONNECTING:

### Arduino

I made up a little wiring harness for the Arduino to connect to the Giraffe through the fake ethernet port. The wiring follows Ethernet T-568B standard:

|PIN | COLOR | FUNCTION|
|----|-------|---------|
|1	 | OW		 | GND     |
|2	 | O		 | 12V     |
|3	 | GW		 | CAN1L   |
|4	 | B		 | N/A     |
|5	 | BW		 | N/A     |
|6	 | G		 | CAN1H   |
|7	 | BRW	 | CAN2H   |
|8	 | BR		 | CAN2L   |

CAN1H and CAN1L should be connected to the MCP2515 CAN H and L lines via Ethernet pins 3 and 6, respectively.
Here's a badly made schematic with little to no effort put into it. RIP, MS Paint.
![alt text](https://github.com/wocsor/corolla_emulator/blob/master/pics/corolla_emu.png?raw=true)

### Radar:

Below is a crude pinout I made for the Radar(Left) and the Giraffe female connector (Right):
￼    ￼

![alt text](https://github.com/wocsor/corolla_emulator/blob/master/pics/radar.png?raw=true)
![alt text](https://github.com/wocsor/corolla_emulator/blob/master/pics/Giraffe.png?raw=true) 



The pins connect as follows:

|RADAR PIN	| FUNCTION	| GIRAFFE PIN|
|-----------|-----------|------------|
|1	      	| 	VCC	    |		4        |
|2			    |  CANL		  |   5        | 
|3			    |  CANH     |		11       | 
|5		      |	CANH      |		12       | 
|6			    |  CANL     |		6        |
|8			    |  GND      |	  1        |


Once this is connected, plug the Arduino into the giraffe by ethernet and upload the sketch to your Arduino. SSH into your Eon using the guide here:
https://community.comma.ai/wiki/index.php/Configuring_OpenPilot

Enter the tmux session with
```
tmux a
```
and create a new window by pressing backtick ``` ` ```, the key above the tab, and C. You can navigate the windows using ``` ` + N``` for next and ``` ` + p``` for Previous.
 
Now you should be able to kill OP in window 0 with control-C and relaunch with:
```
cd /data/openpilot
./launch_openpilot.sh | grep “fingerprinted”
```
Connect everything together and set the switches on the giraffe to 0000. Plug in the 12V power source to the barrel connector of the giraffe. Flip switch 3 to "ON" (1) and you should now see “fingerprinted TOYOTA COROLLA 2017” in the terminal. Openpilot should also start recording and tracking the radar. You’ll know if it worked if you see a big red triangle floating around onscreen (provided the nearest large metal object is a few feet away at least) and the potentiometer affects the speed onscreen. The potentiometer adjusts the wheel speed and the four buttons are for enabling cruise, setting speed, and setting the "car" in drive, respectively. The 4th button currently has no functionality.

## BUGS
- Currently, it's hit or miss with fingerprinting but if you turn off all switches, unplug the barrel connector and plug back in, wait a little, and turn on switch 3 it may work. You can also try running can_printer.py in a separate tmux window to see if the arduino is spitting CAN
- The potentiometer can cause OP to report a negative speed. This is probably easy to fix but I haven't done it yet.
- Enable and Set buttons do nothing. Again, probably not sending a value somewhere. Probably easy to fix again.
