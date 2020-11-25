# Openlane-Sky130-Workshop
An advanced workshop on Sky130 which deals with RTL to GDSII design flow. This workshop helped me to explore on open source tools in Physical design. It gave au thorough understanding of SoC design flow using the open source EDA tools like Google's Skywater which is process design kit on 130nm. Workshop mainly deals with design and characterisation of own standard cell and generating netlist, GDSII flow from RTL. Netlist generation, logic synthesis, placement, floor planning, power planning, timing analysis and routing are done on own design. SPEF extraction, SDC files, library files, cell library files, timing library files and technology files are generated for full design flow. Timing modelling with ideal clock and real clock is done using delay tables, clock tree synthesis,  and signal integrity  is done. Main tools on which complete design flow goes are  OpenSTA, Tritonroute, Yosys, SPEF extractor, NGSPICE and OPENROAD. 

   # Contents:
   ### 1. Introduction
   ### 2. Day 2
   ### 3. Day 3
   ### 4. Day 4
   ### 5. Day 5


   ## 1.Introduction
   Brief about SoC/Processor architecture
   Overview of modules in SoC
   Nominclature of SoC as one of the packages QFN-48 ( Quad Flat No leads 48 pin) with typically size of 7mm x 7mm chip placed in middle of package connected to pins with wire bonds.
 
 ![](Snippets/2CHIP.bmp)
 
   In chip, important components are pads, core and die. These pads are through which we send signals from outside the environment into the chip. Core is placed where all the logic is implemented.
   
![](Snippets/3core.bmp)



