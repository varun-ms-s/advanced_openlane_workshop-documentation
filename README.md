# Advanced OpenLANE Workshop

## Workshop Introduction
This was a 5-day workshop which was extensively conducted using opensource EDA tools(Openlane) by VSDOpen .

### Workshop Timeline






## DAY-1


### TASK
To learn about skywater PDK and implement synthesis stage in openlane.

### LAB

**Design directory**

  Design directory stores all the design files\
  The input files which u want to pass into openlane has to be stored in thos directory\

 ![](day_1/Terminal.PNG)
 
 ![](day_1/Desisgns_files.PNG)

**Openlane flow entry**

![](day_1/openlane_in_1.PNG)

1.Go to openlane flow directory\
 Desktop/work/tools/openlane_working_directory/openlane_flow\
2.Type\
`./flow.tcl -interactive`\
3.Now you will enter to open lane flow and type\
`package require openlane 0.9`

![](day_1/day1_prep_pico.PNG)

4.Pass your design into openflow\
 `prep -design picorv32a` \
-design ->pass the design folder name where you stored your input files to openlane




![](day_1/day1_prep_pico_complete.PNG)
Now openlane basically merges the lef file and technology file and forms a merged file called merged.lef\

5.We are passing picorv32a design into openlane.We can find picorv32a folder in design directory.\
There the verilog input files are stored in src folder.\
Config.tcl is used to set all variables with desired values for openlane flow.\
Runs directory stores all the  run instances.By default run instance name is particular time and date of run.

![](day_1/files_in_picorv_veri.PNG)


Config.tcl has parameters like clock_period,target_density which can be set to desired values


![](day_1/config_in_1.PNG)

6.Type \
run_synthesis \
you will be able to see netlist formed by yosis and abc maps that netlist to skywater pdk cells.\
A yosis reprt will be generated which contains all information of cells used in syntheis file .

![](day_1/day1_buff_cal_1.PNG)


Flipflop ratio=total no of dtxflops/total no of cells = (1634/17323) = 0.094\
Buffer ratio=total no of buufers/total no of cells = ((2247+44)/17323) = 0.132


STA report generated by OpenSta software at end of synthesis

![](day_1/day1_synthesis_complete.PNG)

The new synthesis file will be stored in <designs/picorv32a/runs/"run_file_name"/results/synthesis>

![](day_1/synthesis_file_loc.PNG)

Inside the run instance we find one more config.tcl.This file contains all variables which were set by the user.
![](day_1/config_2.PNG)

In reports directory of the your run instance you can find synthesis directory.Here all reports generated during synthesis stage are stored.

![](day_1/Sunthesis_folder_reports.PNG) 

**PDKs**
In PDK folder of openlane_working_directory you will find three folders. \
Skywater PDK-This is PDK which contaions all standard cells,spice files,library files etc\
Open PDK-This basically consists of scripts which sorts the files in skywater PDK according to particular open source EDA tool.\
SKY130 - Open PDK script creates specific folders for each open source eda tool and places those files from skywater PDK after sorting.\
.
![](day_1/pdks_1.PNG)



## Day-2


### TASK
To implement Floorplan in Openlane and understand the steps to lay good floorplan

**Steps involved in Floorplanning**

1.Define width and height of core and die\
2.Define locations of preplaced cells\
3.Surround Preplaced cells with decoupling capacitors\
4.Power Planning\
5.Pin Placement

**Netlist**-Defines the connectivity of gates\
**Utilization Faztor**=Area occupied by netlist/Area of the core\
**Aspect Ratio**=height/width\

### NOTES
The routing wires consists of parasetic resistance which leads to voltage drop and we could not reach rail to rail voltage at output.**Decoupling capacitors** are used to replenish the charge in a preplaced cell during transition so that we reach rail volatage of VDD.\
Frontend team decides **netlist connectivity** and backened team decides **pin placement**.\
Mostly clock paths are of bigger width comparatevely as they drive most of the cells on core like flipflops ,so it ensures least resistance path.\
Standard Cell palcement happens in placement stage not in floorplan\
Buffers are used in between tracks to replenish the strength of the signal **(signal integrity)**.\
The supply grids are always on the top metal layers as it provides least resistance path. Hence it ensures no losses. You can connect lower metal  layers and top metal layers using vias.

### LAB

1.Go into openlane_flow and follow steps of day.Now we can name our run instance with a user defined name.Here i name it trial_run1 using keyword -tag.

![](/day_2/prep.PNG)

LEFs and technology layer is merged to form merged layer.

![](/day_2/pad_lef.PNG)

To open existing run instance.

![](/day_2/opening_existing_run.PNG)


-overwrite keyword can be used to empty a previously created run instance.

![](/day_2/Overwrite.PNG)

echo command can be used to know the values to which openlane variables are set.

![](/day_2/ECHO_CLK_PERIOD.PNG)

Core_utilization set to 0.65 in config.tcl
![](/day_2/setting_core_utilization.PNG)!

run_floorplan command used to start floorplan

![](/day_2/run_floor.PNG)

floorplan complete

![](/day_2/floorplan_done.PNG)

Floorplan def file is stored under floorplan director in results


![](/day_2/def_floorplan.PNG)


Inside floorplan def file

![](/day_2/def_file_exam.PNG)

those cooardinates are of the form below\
(lbx lby rtx rty)\
lbx-> bottom left x coordinate\
lby-> bottom left y coordinate\
rtx-> top right x cooardinate\
rty-> top left y coordinate

To view floorplan def file we need technolgy file sky130A and merged.lef file\
sky130A is found in <\
merged.lef is found in <

copy both technology and lef file into directory where floorplan is found < \
![](/day_2/got.PNG)

Then go to floorplan directory in results  through terminal\
Type
magic -T sky130A lef read merged.lef def read picorv32a.floorplan.def\
you will get magic layout

![](/day_2/floorplan_magic.PNG)

Config.tcl file is modified to set below parameters of floorplan \
FP_IO_VMETAL->4 (metal4)\
FP_IO_HMETAL->3 (metal3)\
FP_IO_MODE-> 1 (Pins are equidistant)

Here you can see pins are equidistant

![](/day_2/pins_equadistant.PNG)

if FP_IO_MODE-> 2 (pins accumulate in one corner)

![](day_2/fp_io_mode_2.png)


Decoupling capacitors placed in floorplan 

![](/day_2/decapacitors_magic.PNG)

Tapcells

![](/day_2/tapcell.PNG)

We can find all standard cells obtained after synthesis are accumulated in one corner of main core.

![](/day_2/standard_cells.PNG)


Now to start placement type command\
run_placement

![](/day_2/placement_2.PNG)

While placement going on we come across two words:-HPWL and OVFL\
HPWL-Half perimeter wire length is just a model to approximate the wire lengths inorder to use it as parameter to optimise placement\
OVFL-This is overflow parameter which should decrease as placement optimization takes place\

![](/day_2/hpwl_opt.PNG)

Final placement pass 

![](/day_2/placement_pass.PNG)

Placement def file is stored in following <\
Copy SKY130A tech file and merged.lef to that folder where u find placement def\
Through terminal go to the directory of placement def file\
Type \
magic -T sky130A lef read merged.lef def read picorv32.placement.def\

Placement

![](/day_2/standard_cells_placement_1.PNG)

Placement at 0.8 target density (which i set using COMMAND <set ::env(PL_TARGET_DENSITY) 0.8>

![](/day_2/0.8_density.PNG)

Placement at 0.4 target density (which i set using COMMAND <set ::env(PL_TARGET_DENSITY) 0.4>

![](/day_2/0.4_targ_den.PNG)






































