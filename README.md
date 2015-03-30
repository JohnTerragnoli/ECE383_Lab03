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
4. **Adding the Muxes:**  The muxes were added to the datapath file in the ISE text editor using the same repetitive and compilation process mentioned in step three.  There were no issues here, except for the annoying casting errors which kept popping up.  After this was done, the three copies files were then added into microblaze.  I started to generate the bitstream, which was created perfectly, and then I uploaded it, and nothing worked at all.  Because exSel is set to zero, it was supposed to be running lab2 functionality, theoretically.  I decided to check out a couple of options below for the cause.  
5. **Copying Files into Microblaze:** It might have been an issue that another file added earlier, like video, or something like that, was tampered with, which is why no signals are showing up on the screen correctly.  I got my video and other lower level files, and copied them back into the microblaze and compiled it again.  Nothing changed unfortunately.  
6. **Moving Triggers:** Ignored previous issue for now just to get the triggers moving around on the screen so I could say that something works right. This only involved a little c code in SDK editor and changing the lab2 instantiation in user_logic.vhd.  What I had to do was assign slave registers to different parts of the instantiation, including read and write registers. I got this working pretty well, and they move according to button presses on the computer keyboard now.  At least I know that the UART is working and that I can communicate values between my computer and the FPGA.  
7. **Checking Warnings:** I then followed Dr. York's advice to look at the warnings popping up in SDK.  One of them said that region "microblaze_0_i_bram_ctrl_microblaze_0_d_bram_ctrl" overflowed by 16 bytes.  Therefore, I tried changing, in the addresses tab, the size of the microblaze_0 to be 32k each.  Then I compiled it again and nothing changed.  I decided to change them back to 16k.
8. **Inputs and Outputs:** So originally I had SDATA_IN and BIT_CLK as set to outputs.  When I changed them to inputs however, I forgot to delete the instance of the peripheral so that the new changes can be implemented.  I had no idea that this would make a difference, but apparently it does! Now I know.  When I compiled it, however, it did not work correctly. I looked through my code again, especially the Xilinx hardware portion, not the peripheral, and realized that I had two unneeded ports declared throughout my code.  We deleted the extra ports and then code compiled still, although it did not work.  I left the ports out, however, as it could have been working with other errors to obstruct success.  
9. **New Project:** While looking back on the warnings, Dr. Coulston and I noticed that they were quite unusual.  In particular, there was a warning which had to do with the length of one of the axi registers.  Under the "System Assembly View" tab, some of the microblaze instantiations seemed to be out of order.  Dr. Coulston recommended that I made a new microblaze project and then add another copy of my periperal to that project.  He thought my original project might have been created wrong or something.  When I did this, however, nothing changed and the oscilloscope still did not show a signal on the screen.  
10. **Reading Lbus_out and Rbus_out:** I thought it would be a smart idea to examine the inside of my lab2 code to figure out where the process was being held up.  Since the 0th and 1st slave register represent Lbus_out and Rbus_out, I tried to use them while a signal was being input into the FPGA, to see if they were actually changing.  I hooked up a key on my keyboard to printing the buses' values on the terminal.  Turns out all of them were consistently zero, meaning the AC97 was messed up in some way or the slave registers were hooked up wrong.  I checked the slave registers and they looked like they were right. I then compared my AC97 instantiation to Sabins.  Looked about right too.  I decided to check other parts of my code against Dr. York's and Sabin's.  
11. **PAO File:** I looked at my PAO file because I remember that giving me trouble earlier.  I compared my .pao to Dr. York's.  It looked like I was missing two lines, shown below: 

```
##lib oscilloscope_2_v1_00_a ac97_cmd.vhd
##lib oscilloscope_2_v1_00_a ac97_wrapper.vhd
```
I tried generating a bitstream, but an error came within the first few seconds.  I thought I might be missing the files, but I checked to see if the files were included in Sabin's code, and they weren't there.  So if his code was working without them then surely I did not need them, especially because my Lab02 code worked without them. I decided to remove the .pao files and at least the gitstream generated again, even though it didn't work still.  

1. **Starting Over:** A couple of days into spring break I started getting a little desperate, especially because there was no one there to help answer my technical questions.  I kept all of my old code, but decided to start all over, as if I were doing it for the first time.  This included checking in ISE Project editor to make sure lab02 was still working.  
2. **Checking ISE Project Editor:** I ran this code to make sure I could still read something on my oscilloscope and move the triggers around with the buttons on the Xilix board.  I got these files from gitHub, because the Lab02, lab2_pack, and datapath files had all been altered to accomodate for Lab03.  It worked again like new.  
3. **New Xilinx Project:** Then I created a new project name in Xilinx Platform Studio for lab03c.  This made sure to include the correct directories, the UART, and the appropriate peripherals.  
4. **Adding the UART:**  In my last project, the reason the R and L bus kept showing up as blank could have been because I hooked up the UART wrong.  I decided to be extra careful in how I did it the second time.  I was sure to add the UART lite from the "Communication Slow Speed", to change its base address to 0x84000000, and add the following lines to the .ucf file.  
 ```
 net axi_uartlite_0_RX_pin LOC=A16 | IOSTANDARD = LVCMOS33;

net axi_uartlite_0_TX_pin LOC=B16 | IOSTANDARD = LVCMOS33;
```
I generated the bitstream successfully then uploaded it to SDK for a test. 

5. **Testing Echo:** I used the code from the tutorial (lesson 17) to ensure that my UART was working correctly.  The code used can be shown below: 


```
#include <xuartlite_l.h>
#include <xparameters.h>

int main(void)
{
 while (1)
 {
  unsigned char c;
  c = XUartLite_RecvByte(0x84000000);
  XUartLite_SendByte(0x84000000, c + 2);
 }

 return 0;
}
```
The output from this code was 3 if you input a 1, and a 4 if a 2 was input.  Therefore, we know the board was getting a character, then adding "2" to it, then returning that value.  Looks like the UART is working properly.  

6. **Adding OScope Peripheral:**  Tried again to create this peripheral.  This was done carefully and everything that I did is written below.  Clicked the "hardware -> import peripheral -> create template for new peripheral -> to EDK repository (left default) -> named it oscope_3 -> AXI-4LITE -> only "include data phase timer" and user logic software register" checked" -> made the number of software accessible registers to be 16 -> (next) -> (next) -> "generate ISE and XST Project files" checked -> (finish).  I created 16 slave registers so it would be easier to talk and so different pieces of data would not have to be mixed.  
7.  **Adding OScope Instance:** This was similar to creating the UART instance, except the base address was 0x83000000, and the .ucf was not altered just yet because not all the appropriate files were added and altered.  The following files were added.  

**Adding Files**
- ac97.vhd
- BRAM_counter.vhd
- comparator.vhd
- counter.vhd
- counter_glue.vhd
- dvid.vhdl
- flagRegister.vhd
- h_synch.vhd
- lab2.vhd
- lab2_datapath.vhd
- lab2_fsm.vhd
- lab2_pack.vhdl
- scopeFace.vhd
- tdms.vhdl
- vga.vhd
- video.vhdl
- v_synch.vhd

After doing this, I altered the .mpd, .mhs, .ucf, user_logic.vhd, .pao, and oscope_3.vhd of the oscope peripheral.  It did not bother me that I did not change the instantiations just yet for the buttons' sake.  I just really needed something to show up on the screen when a peripheral was used.  

**.mpd** The .mpd file was changed by adding in the correct ports to the end of the port section.  The code added can be seen below: 

```
### I added this#######################################
PORT SDATA_IN = "", DIR = I
PORT BIT_CLK = "", DIR = I
PORT SYNC = "", DIR = O
PORT SDATA_OUT = "", DIR = O
PORT AC97_n_RESET = "", DIR = O
PORT tmds = "", DIR = O, VEC=[3:0]
PORT tmdsb = "", DIR = O, VEC=[3:0]
########################################################
```

**.mhs** I saved the file and moved onto the .mhs file.  Technically this should update on it's own, but I clicked the "rescan user repositories" and checked the .mhs just in case.  It appears to have updated correctly on its own.  

**.ucf** Next I moved onto the .ucf file, which was largely copied from lab2.  The following lines of code were added to the .ucf file:

```
NET "oscope_3_0_tmds_pin(0)"	LOC = "D8" | IOSTANDARD = TMDS_33 ; # Blue
NET "oscope_3_0_tmdsb_pin(0)"	LOC = "C8" | IOSTANDARD = TMDS_33 ;
NET "oscope_3_0_tmds_pin(1)"	LOC = "C7" | IOSTANDARD = TMDS_33 ; # Red
NET "oscope_3_0_tmdsb_pin(1)"	LOC = "A7" | IOSTANDARD = TMDS_33 ;
NET "oscope_3_0_tmds_pin(2)"	LOC = "B8" | IOSTANDARD = TMDS_33 ; # Green
NET "oscope_3_0_tmdsb_pin(2)"	LOC = "A8" | IOSTANDARD = TMDS_33 ;
NET "oscope_3_0_tmds_pin(3)"	LOC = "B6" | IOSTANDARD = TMDS_33 ; # Clock
NET "oscope_3_0_tmdsb_pin(3)"	LOC = "A6" | IOSTANDARD = TMDS_33 ;

NET "oscope_3_0_BIT_CLK_pin" LOC = L13;
NET "oscope_3_0_SDATA_IN_pin" LOC = T18;
NET "oscope_3_0_SDATA_OUT_pin" LOC = N16;
NET "oscope_3_0_SYNC_pin" LOC = U17;
```

As this was done, I went into the system assembly view and made the ports on the oscope peripheral to be external. 


**user_logic** Then I updated the user_logic.vhd file, which included adding the user ports, internal signals, which would be used later, a lab2.vhd instantiation, and would later involve changing the first three read slave registers.  Note, the lab2 instantiation was not perfect the first time, as I just wanted to get a waveform to show up on the screen.  Baby steps.  

The added user ports are shown below: 
```
	 SDATA_IN							  : in std_logic;
	 BIT_CLK								  : in std_logic;
	 SYNC								  	  : out std_logic;
	 SDATA_OUT							  : out std_logic;
	 AC97_n_RESET						  : out std_logic;
	 tmds								  	  : out std_logic_vector(3 downto 0);
	 tmdsb								  : out std_logic_vector(3 downto 0);
```
	 
The added internal signals are shown below: 
```
	signal exWr : std_logic_vector(9 downto 0);
	signal exWen : std_logic;
	signal exSel : std_logic;
	signal Lbus_out : std_logic_vector (15 downto 0); 
	signal Rbus_out : std_logic_vector (15 downto 0); 
	signal exLbus : std_logic_vector (15 downto 0);  
	signal exRbus : std_logic_vector (15 downto 0);  	  
	signal flagQ : std_logic_vector(7 downto 0);  	 
	signal flagClear : std_logic_vector(7 downto 0);  	 
```

The lab2.vhd instantiation is shown below: 
```
lab02 :  lab2 
    Port map ( 
     clk => Bus2IP_Clk,
     reset => Bus2IP_Resetn,
     SDATA_IN => SDATA_IN,
     BIT_CLK=> BIT_CLK, 
     SYNC =>SYNC,
     SDATA_OUT =>SDATA_OUT,
     AC97_n_RESET => AC97_n_RESET,
     tmds  => tmds,
     tmdsb => tmdsb,
     btn => "00000",
     JB => open);
```

The lines for the first three slave registers are shown below: 

```
      when "1000000000000000" => slv_ip2bus_data <= X"0000" & Lbus_out;			--slv0
      when "0100000000000000" => slv_ip2bus_data <= X"0000" & Rbus_out;			--slv1
      when "0010000000000000" => slv_ip2bus_data <= X"000000" & flagQ ;			--slv2
```

**.pao** As for the .pao file, the files used in the program had to be added in order from internal to external.  The order which was chosen is shown below: 

```
lib proc_common_v3_00_a  all 
lib axi_lite_ipif_v1_01_a  all 

lib oscope_3_v1_00_a lab2_pack.vhdl
lib oscope_3_v1_00_a counter.vhd
lib oscope_3_v1_00_a counter_glue.vhd
lib oscope_3_v1_00_a h_synch.vhd
lib oscope_3_v1_00_a v_synch.vhd
lib oscope_3_v1_00_a scopeFace.vhd
lib oscope_3_v1_00_a tdms.vhdl
lib oscope_3_v1_00_a vga.vhd
lib oscope_3_v1_00_a dvid.vhdl
lib oscope_3_v1_00_a video.vhdl

lib oscope_3_v1_00_a ac97.vhd

lib oscope_3_v1_00_a BRAM_counter.vhd
lib oscope_3_v1_00_a comparator.vhd
##lib oscope_3_v1_00_a flagRegister.vhd
lib oscope_3_v1_00_a lab2_datapath.vhd
lib oscope_3_v1_00_a lab2_fsm.vhd
lib oscope_3_v1_00_a lab2.vhd
lib oscope_3_v1_00_a user_logic.vhd
lib oscope_3_v1_00_a oscope_3.vhd
```

Notice that all of the files which were added are present somewhere in the .pao file.  Also, the flagRegister.vhd file is commented out, because there is no instantiation of the flagRegister in lab2 yet.  This will be done for A Functionality.  Right now something just needs to show up on the screen.  

**oscope_3.vhd**  Last but not least, the oscope_3.vhd file was updated by adding the appropriate user ports and mapping them.  


User Ports Added: 
```
	SDATA_IN : in std_logic; 
	BIT_CLK : in std_logic; 
	SYNC : out std_logic; 
	SDATA_OUT : out std_logic; 
	AC97_n_RESET : out STD_LOGIC;
	tmds : out  STD_LOGIC_VECTOR (3 downto 0);
	tmdsb : out  STD_LOGIC_VECTOR (3 downto 0);
```

User Ports Maped: 
```
	SDATA_IN =>SDATA_IN,
	BIT_CLK => BIT_CLK,
	SYNC => SYNC,
	SDATA_OUT=>SDATA_OUT,
	AC97_n_RESET=> AC97_n_RESET,
	tmds =>tmds,
	tmdsb => tmdsb,
```

Once all of these changes were made to the files .mpd, .mhs, .ucf, user_logic.vhd, .pao, and oscope_3.vhd, the bitstream was almost generated.  Then I remembered that last time I almost had an issue with the libraries on my datapath and lab2.  I decided to compare mine with the ones Sabin used to ensure I was not making a careless error.  Lab2 and datapath were fine, but my fsm machine file had different libraries than he did.  

Mine had 
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
library UNISIM;
use UNISIM.VComponents.all;
```

Sabin's Had: 
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
library UNIMACRO;
use UNIMACRO.vcomponents.all;
use work.lab2Parts.all;		
```

I decided to use the union of two, shown below: 
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
library UNISIM;
use UNISIM.VComponents.all;
library UNIMACRO;
use UNIMACRO.vcomponents.all;
use work.lab2Parts.all;		
```

After these libraries were added, I tried to generate a bitstream in this project and it worked.  I then added this C code, which hopefully does nothing.  The idea is that it should run exactly the way it did in lab02.  
```
#include <xuartlite_l.h>
#include <xparameters.h>

int main(void)
{
 while (1){


//  unsigned char c;
//  c = XUartLite_RecvByte(0x84000000);
//  XUartLite_SendByte(0x84000000, c + 2);


 }//end while

 return 0;
}
```

When I did this, nothing showed up on the screen.  I decided to run one of my first tries at this project again.  When I did, at least a grid showed up on the screen from the FPGA.  Plus, the triggers moved back and forth.  I wanted to figure out the difference between these two projects, why one could put something on the screen and the other could not.  First, I made sure that all of the files in the old project were present in the new one.  This was the case.  Then, in the new project, I decided to check what was going into the modules which ultimately produce the image on the screen.  Something there has to be different.  

As I was comparing the new project I just created to the old one I abandoned, I noticed that the instantiation for my old project was not complete.  The Rbus_out and Lbus_out signals were not hooked up to the instantiation.  This made sense as to why the values for Rbus_out and Lbus_out were always showing up as zero then.  I decided to generate the bitstream for this old project after making the change to the instantiation.  Little did I know, that when I uploaded these changes to the FPGA, a little yellow showed up on the screen as I plugged in my audio jack.  I tried a signal, but nothing showed up.  I made the signal much louder, and then I saw that the upper half of the signal was in fact there.  The trigger appeared to be working, however, the signal seemed to be shifted much lower on the screen.  After noticing this, however, I just decided to stick with this old project I made.  

**Fixing the Shift:**  I figured that this was probably caused by the trigger shift used in Lab02 to get the signal to set right.  If I changed this everything would probably go back to normal.  When I made the changes, only slight number changes in the C code were required to get the oscilloscope to trigger the same way it did in lab02.  


**Programming exSel:**  I wanted to be able to toggle back and forth between Lab02 and Lab03 functionality using the exSel signal.  This just required some button logic in the C code, seeing that the exSel signals were already set up in the hardware code.  When the exSel is turned on, then for the time being the signal should just freeze, since it's triggering mechanisms will not work and BRAM will not be filled appropriately.  The following C code was added to make 'x' the toggle button.  

```
    case 'x':

    	if(Xil_In32(exSel)==0x00000001){
    		Xil_Out32(exSel,0x00000000);
    		xil_printf("	exSel  = %x\r\n",Xil_In32(exSel));


    	}else if(Xil_In32(exSel) == 0x00000000){
    		Xil_Out32(exSel,0x00000001);
    		xil_printf("	exSel  = %x\r\n",Xil_In32(exSel));
    	}
    break;
```

When I ran this code and pressed 'x' a couple of times, the exSel did not freeze but the exSel itself was changing, as noted by the picture below: 

![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE383_Lab03/master/Pictures/Toggle%20Slave%20Works.PNG "toggle almost works")

This means that the slave register responding to the toggle switch is at least working.  It is probably the internal exSel logic which is not working then.  

So to check this I decided to go through the modules user_logic.vhd, lab2.vhd, and lab2_datapath.vhd, just to make sure the exSel signal was getting through properly.  I found a minor error where I forgot to pass exSel into the datapath.  I fixed this, generated the bitstream again, and tried running the toggle test again.  It worked!  The signal froze when the exSel was turned on.  



Replace the fsm logic with C code logic.  

have a table for all of the slave registers!!!


**These decisions might explain why this was not working:**


**Draw Contant Using Slave Registers:**  Wanted to make sure that when I enabled the exLbus, exRbus, exSel, exWen, and exWrAddr, I could hardcode an image onto the monitor.  

I tried adding this code to hardcode a horizontal and diagonal lines. 

```
void testWrite(){
	int i;

	for(i = 0; i<620; i++){
		Xil_Out32(exLbus,i);
		Xil_Out32(exRbus,centerVolt-20);
		Xil_Out32(exWrAddr,i);
		Xil_Out32(exWen,1);
		Xil_Out32(exWen,0);
	}
}
```

Oddly enough, the sinusoid just dissapeared when I turned the exSel on.  I tried messing around with the numbers I put into the C code.   In the "for" loop, I made the lower and upper bounds lowerVolt and upperVolt.  When I did this, I got the left half of the screen as a sinusoid and the other half blank.  This did not make a ton of sense, because it should have just drawn two straight lines.  I went back to check how my exRbus and ExLbus signals were being hooked up, to make sure they were actually feeding into the video module.  



