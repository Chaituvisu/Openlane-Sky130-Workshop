# Openlane-Sky130-Workshop
An advanced workshop on Sky130 which deals with RTL to GDSII design flow. This workshop helped me to explore on open source tools in Physical design. It gave a thorough understanding of SoC design flow using the open source EDA tools like Google's Skywater which is process design kit on 130nm. Workshop mainly deals with design and characterisation of own standard cell and generating netlist, GDSII flow from RTL. Netlist generation, logic synthesis, placement, floor planning, power planning, timing analysis and routing are done on own design. SPEF extraction, SDC files, library files, cell library files, timing library files and technology files are generated for full design flow. Timing modelling with ideal clock and real clock is done using delay tables, clock tree synthesis,  and signal integrity  is done. Main tools on which complete design flow goes are  OpenSTA, Tritonroute, Yosys, SPEF extractor, NGSPICE and OPENROAD. 

   # Contents:
   #### 1. INTRODUCTION TO SoC & SYNTHESIS
   #### 2. FLOORPLANNING & PLACEMENT 
   #### 3. STANDARD CELL_INVERTER LAYOUT & DRC
   #### 4. STATIC TIMING ANALYSIS & CLOCK TREE SYNTHESIS
   #### 5. ROUTING & SPEF EXTRACTION


## 1.INTRODUCTION TO SoC & SYNTHESIS
   Brief about SoC/Processor architecture
   Overview of modules in SoC
   Nominclature of SoC as one of the packages QFN-48 ( Quad Flat No leads 48 pin) with typically size of 7mm x 7mm chip placed in middle of package connected to pins with wire bonds.
 
 ![](Snippets/2CHIP.bmp)
 
   In chip, important components are pads, core and die. These pads are through which we send signals from outside the environment into the chip. Core is placed where all the logic is implemented.
   
![](Snippets/3core.bmp)

   
   RISC-V architecture has interface with different modules like analogue IPs, macros like digital blocks, SRAM, ADCs and DACs. These all are designed using verilog(.v file) and these files are usually RTL files which are used to generate netlist which further used for generation placement & routing. Placement and routing is done which also includes floor planning and power planning.
   
  ![](Snippets/4risc-v.bmp)
  
  All the apps are written in C language which is not understood by machine or processor. This language is converted into binary/ assemble language. In order to make processor understand the process, there is interface that is present in between RISC-V and layout flow is hardware/physical layer which deals with verilog hardware language. In this process, processor spec using RTL generates netlist and using library files & tech files and finally P&R flow is implemented
  
 ![](Snippets/6software.jpg)
 
 ![](Snippets/7HARDWAREDESCR.jpg)
 
 
   Basic RTL is converted to circuit, out of components from standard cell library. These library cells are typically regular layouts with fixed height in a rectangle. Each cell has different views/models
   In floor planning, partition the chip die between different system building blocks and IO pads. Macros floor planning dimensions, pin locations, rows definitions. Power planning is done on upper metal layers because those are thicker than lower metal layers. Hence less resistance for power loss. 
   Placement is done in two steps: global & detailed.  Global finds optimal position for all cells.
   
   
 ![](Snippets/9RTL2GDSII.jpg)
 
 ![](Snippets/10power_planning.jpg)
 
   Before synthesis, we have to prepare the design where it merges both files - CELL level and TECH level into one.
   First, preparation of the design in OpenLANE merges the technology LEF and cell LEF files. Tech LEF file contains layer definitions and a set of restricted design rules needed for PnR flow. The cell LEF  file has obstruction information of each standard cell needed to minimize DRC errors during PnR flow:
   
  Invoking Openlane using below commands in the file path:
               
               /home/chaitanya/Desktop/vsdflow/work/tools/openlane_working_dir/openlane/
               
   Below commands to invoke openlane:
   
                  docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) openlane:rc2
                  
   /flow.tcl is the script which runs the OpenLANE flow and also we can run in autonomous mode. In order to run step wise we can use " -interactive" mode
   
               In bash: ./flow.tcl -interactive
               
   Below commands are used to prepare design and tag them to folder for further analysis.
   
               prep -design picorv32a -tag workshop
               
 
 ![](Snippets/Day1flow_tcl.png)
 
 
 In run folder we can see the merge file got created, where we get CELL & TECH level info.
 
               /home/chaitanya/Desktop/vsdflow/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/run29_1
 

 ![](Snippets/Day1Synthesis.png)
 
 ![](Snippets/Day1Synthesis_1.png)


## 2. FLOORPLANNING & PLACEMENT 

In order to find the size of chip/block and Aspect ratio, the core area and IO core spacing and Floor Plan and Power Network with horizontal metal layer. IO Placement/Pin placement and I/O Pin Rings for Blocks. Using the command in openlane floorplan is run.

                     % run_floorplan

![](Snippets/Day2_floorplan.png)

![](Snippets/Day2_Floorplan_Area.png)

After floorplan is run ,magic command for floorplan.def file :
               
In the path we have to enter the command /openlane/designs/picorv32a/runs/run29/results/floorplan$ 
         
         magic -T /home/chaitanya/Desktop/vsdflow/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def
         
 For viewing metal layers and pin we have to zoom in for the pins using  'z' and for zoom out 'shift+z'


![](Snippets/Day2_Floorplan_Vmetal.png)


For IO_MODE, we have to enable the switch: Enabling the IO pins using a switch with a parameter called  FP_IO_MODE

![](Snippets/DAY3_FLOORPLAN_IO_MODE_2.png)

![](Snippets/Day2_Floorplan_HMetal.png)

![](Snippets/Day2_Floorplan_STDCELL.png)


## 3. STANDARD CELL_INVERTER LAYOUT & DRC

   ### Git clone the standard cell from VSDSTDCELL design
   
   
![](Snippets/DAY3_INV_LAYOUT_1.png)

  
  For analysing the inverter response by giving a pulse input at the input port A and plugging at output port Y.
 here below in image we can see the process of runinng NGSPICE and plotting the response and STANDARD CELL- INVERTER_TRANS CHARACTERISTICS
 
 ![](Snippets/DAY3_INV_SPICE.png)
 
   
![](Snippets/DAY3_INV_RUN_PLOT.png)

After doing analysis and layout the parasitic extraction is done using the steps in the image.

               Commands:
               
                     % extractall
                     % ext2spice cthresh 0 rthresh 0   //which extracts all parasitics from threshold 0
                     % ext2spice   // which creates spice file for access.

![](Snippets/DAY3_INV_SPICE_EXTRACTION.png)


  ### Identification of NMOS and PMOS in LAYOUT:
  
  ![](Snippets/DAY3_INV_LAYOUT_1.png)

 DRC tests: The main objective of this paper is to explain the various types of design rule checks (DRC) violation, their causes and how to fix the various design rule checks (DRC) at lower technology node on block level as well as full chip level implementation while meeting the design rule with respect to latest technology standards.
 
 ![](Snippets/DRC_TEST.png)
 
 ![](Snippets/DAY3_DRC_TESTS.png)
   
 
 
 
 
 
 ### 4. STATIC TIMING ANALYSIS & CLOCK TREE SYNTHESIS
 
 Inserting the standard cell into picorv32a and doing STA and CTS. 
 
   ![](Snippets/DAY4_PORT_DECLARATION.png)
    
   
   ![](Snippets/DAY4_PLACEMENT.png)
  
   
   Lef file gives info of IP input, output, power and ground . It protects IP
   magic command for placement.def file in the openlane_working_dir/openlane/designs/picorv32a/runs/run29/results/placement$ 
   
   
         magic -T /home/chaitanya/Desktop/vsdflow/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
   
  
   
   The above file is nothing but used during routing stage. Routes are basically metal traces. Tracks can do P & R to routes in file:
                  
                        Li1  X 0.23 (x-origin)  0.46 (y-spacing)
                        Li1  y 0.17 (y-origin)  0.34 (y-spacing)
    
   ![](Snippets/DAY4_TRACKS_INFO.png)
   
   ![](Snippets/DAY4_PITCH_OFFSET.png)
                        
   ![](Snippets/DAY4_VSDINV_IN_PICORV.png)
   
   
 Clock tress synthesis is done after inserting standard cell into the design- picorv32a. After running floorplan and standard cell placement in OpenLANE we are ready to insert our clock tree for sequential elements in our design.
 
 In this stage clock buffers get added,we can check the folder in the path:
 
                     openlane_working_dir/openlane/designs/picorv32a/runs/run29/results/synthesis/picorv32a.synthesis_cts.v
 
                     
       
  
  ![](Snippets/DAY4_CTS.png)
  
  
 
 
 ### 5. ROUTING & SPEF EXTRACTION
 
 
 After generating our clock tree network and verifying post routing STA checks we are ready to generate the power distribution network in OpenLANE. For standard cell rows we need power and ground. Here we can see the std cell rail width and pitch. At this width & pitch we have to plug the inverter where it matches the power rails width & pitch in order to get connection with power. From power pads we get to straps and rails down to std cells. Straps are at metal 4 & metal 5.
 
                        % run_routing
                        
   Routing is catergorised into two: a) Fast route b) Detailed route.
   Fast route is followed by detailed route that it needs to realise segments & VIA in accordance with global route.
   
   Now after routing parasitics come into picture in order to extract them an extractor is needed.  SPEF EXTRACTOR is  present in  /work/tools/SPEF EXTRACTOR
   
   using the command spef file is extracted:
   
                  $ python3 main.py  /home/chaitanya/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/run29_1/tmp/merged.lef  def /home/chaitanya/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/run29_1/results/routing/picorv32a.def
                  
     SPEF file is created in same location as of def file in routing folder.
