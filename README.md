# Advanced OpenLANE Workshop

## Workshop Introduction
This was a 5-day workshop which was extensively conducted using opensource EDA tools(Openlane) by VSDOpen .

### Workshop Timeline






## DAY-1

**Design directory**

  Design directory stores all the design files\
  The input files which u want to pass into openlane has to be stored in thos directory\

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
prep -design picorv32a \
-design ->pass the design folder name where you stored your input files to openlane\




![](day_1/day1_prep_pico_complete.PNG)
Now openlane basically merges the lef file and technology file and forms a merged file called merged.lef\

5.We are passing picorv32a design into openlane.We can find picorv32a folder in design directory.\
There the verilog input file is stored in src folder.Config.tcl is used to set all variables with desired values for openlane flow

![]day_1/files_in_picorv_veri.PNG

















