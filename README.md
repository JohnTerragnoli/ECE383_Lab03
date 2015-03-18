# ECE383_Lab03

#Purpose
The purpose of this lab was to recreate the oscilloscope made in lab2 using a microblaze controller in place of the finite state machine.  Also, both the triggerVolt and triggerTime will be needed to set the signal on the screen.  

#Planning

For lab 2, the following block diagram was used in planning:

 ![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE383_Lab03/master/Pictures/lab2Arch.jpg "lab02 schematic")

For the most part, this same block diagram was used.  However, some modifications were made.  These changes are described by the schematic below:  

![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE383_Lab03/master/Pictures/Complete%20Schematic.JPG "lab03 schematic")

Most notably, the finite state machine was removed and was to be replaced by the microblaze.  Also, the button logic was going to be written in the c code instead of VHDL.  Additionally, the way the information was stored and triggered also needed to be changed to incorporate a circular que, although this is not evident from the schematic.  


The design process along with everything I learned can be seen below: 

#Process
1. **Copying lab2 files into microblaze**  This would seem like an easy task, however, a lot of issues presented themselves.  Fortunately I learned a lot from this.  
