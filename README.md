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
1. **Copying Lab2 Files into Microblaze:**  This would seem like an easy task, however, a lot of issues presented themselves.  Fortunately I learned a lot from this.  First, the peripheral oscilloscope had to be created, since this is where the .vhd and .vhdl files would be placed for the project.  This was just like the tutorial and no errors occured.  Then I copied the appropriate files in from lab02.  Then I tried to create the bitstream.  Unfortunately, this took forever and I still got multiple errors.  The first errors were cryptic and hard to understand, but after asking C2 Jeremy Gruszka he pointed out that I needed to remove some of the librarys in lab2 and user_logic for it to compile correctly.  I followed his instructions and some of the errors went away, but not all of them.  I gave Jeremy a high five anyway.  I couldn't figure out all of the problems associated with the compilation.  However, I decided that a number of the issues could have arisen when I tried to add the muxes into the lab02 code.  I tried running my lab02 code again through ISE Project Editor, and it did not work.  I fixed this as shown below: 
2. **Fixing ISE Project Editor:** Luckily, I uploaded all of my files from lab02 to github right after I finished that lab.  I realized that the only files I edited were Lab02, lab2_datapath, and lab2_pack.  I just copied the code from the online files into ISE Project Editor.  Upon uploading to the FPGA again, it worked (thankfully).   I decided then to make the changes to the files in ISE Project Editor and testing it in the editor before turning it over, since ISE compiles faster.  
3. **Changing Lab2 Files:** Most importantly, the entity, declaration, and the instantiations of the datapath and lab2 all had to be changed.  These changes were done slowly in ISE, as I messed up the first time by being too quick, and I compiled in between each change.  With each iteration the project compiled and created a working oscilloscope on the FPGA.  
4. **Adding the Muxes:**  The muxes were added to the datapath file in the ISE text editor using the same repetitive and compilation process mentioned in step three.  There were no issues here.  
