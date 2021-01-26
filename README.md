# Advanced OpenLANE Workshop

## Workshop Introduction
This was a 5-day workshop which was extensively conducted using opensource EDA tools(Openlane) by VSDOpen .

### Workshop Timeline






## DAY-1

**Design directory**

  Design directory stores all the design files

 ![](day_1/Terminal.PNG)
 
 ![](day_1/Desisgns_files.PNG)

**Openlane flow entry**

1.Go to openlane flow directory\
 Desktop/work/tools/openlane_working_directory/openlane_flow\
2.Type\
./flow.tcl -interactive\
3.Now you will enter to open lane flow and type\
package require openlane 0.9

![](day_1/day1_prep_pico.PNG)

4.Pass your design into openflow\
prep -design picorv32a -tag trial_run1\
-design ->pass the design folder for your input files to openlane\
-tag -> Save your run with a well_defined name of your choice\

![](day_1/day1_prep_pico_complete.PNG)










