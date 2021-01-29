# Advanced OpenLANE Workshop

## Workshop Introduction
This was a 5-day workshop which was extensively conducted using opensource EDA tools(Openlane) by VSDcorp .



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

Go to openlane flow directory\
 Desktop/work/tools/openlane_working_directory/openlane_flow\
Type `./flow.tcl -interactive`\
Now you will enter to open lane flow and type `package require openlane 0.9`

![](day_1/day1_prep_pico.PNG)

Pass your design into openflow\
`prep -design picorv32a` \
-design ->pass the design folder name where you stored your input files to openlane




![](day_1/day1_prep_pico_complete.PNG)
Now openlane basically merges the lef file and technology file and forms a merged file called merged.lef\

We are passing picorv32a design into openlane.We can find picorv32a folder in design directory.\
There the verilog input files are stored in src folder.\
Config.tcl is used to set all variables with desired values for openlane flow.\
Runs directory stores all the  run instances.By default run instance name is particular time and date of run.

![](day_1/files_in_picorv_veri.PNG)


Config.tcl has parameters like clock_period,target_density which can be set to desired values


![](day_1/config_in_1.PNG)

Type `run_synthesis` \
you will be able to see netlist formed by yosis and abc maps that netlist to skywater pdk cells.\
A yosis reprt will be generated which contains all information of cells used in syntheis file .

![](day_1/day1_buff_cal_1.PNG)


`Flipflop ratio=total no of dtxflops/total no of cells = (1634/17323) = 0.094`\
`Buffer ratio=total no of buufers/total no of cells = ((2247+44)/17323) = 0.132`


STA report generated by OpenSta software at end of synthesis

![](day_1/day1_synthesis_complete.PNG)

The new synthesis file will be stored in `<designs/picorv32a/runs/"run_file_name"/results/synthesis>`

![](day_1/synthesis_file_loc.PNG)

Inside the run instance we find one more config.tcl.This file contains all variables which were set by the user.
![](day_1/config_2.PNG)

In reports directory of the your run instance you can find synthesis directory.Here all reports generated during synthesis stage are stored.

![](day_1/Sunthesis_folder_reports.PNG) 

**PDKs** 

In PDK folder of openlane_working_directory you will find three folders. \
**Skywater PDK**-This is PDK which contaions all standard cells,spice files,library files etc\
**Open PDK**-This basically consists of scripts which sorts the files in skywater PDK according to particular open source EDA tool.\
**SKY130** - Open PDK script creates specific folders for each open source eda tool and places those files from skywater PDK after sorting.\
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


`-overwrite` keyword can be used to empty a previously created run instance.

![](/day_2/Overwrite.PNG)

`echo` command can be used to know the values to which openlane variables are set.

![](/day_2/ECHO_CLK_PERIOD.PNG)

Core_utilization set to 0.65 in config.tcl
![](/day_2/setting_core_utilization.PNG)!

`run_floorpla`n command used to start floorplan

![](/day_2/run_floor.PNG)

floorplan complete

![](/day_2/floorplan_done.PNG)

Floorplan def file is stored under floorplan directory in results


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
sky130A is found in `<\openlane_working_dir\pdks\sky130A\libs.tech\magic>`\
merged.lef is found in `<\designs\picorv32a\runs\trail_run1\temp\>`

copy both technology and lef file into directory where floorplan is found `< \designs\picorv32a\runs\trail_run1\results\floorplan>`
![](/day_2/got.PNG)

Then go to floorplan directory in results  through terminal\
Type
`magic -T sky130A lef read merged.lef def read picorv32a.floorplan.def`\
you will get magic layout

![](/day_2/floorplan_magic.PNG)

Config.tcl file is modified to set below parameters of floorplan \
FP_IO_VMETAL->4 (metal4)\
FP_IO_HMETAL->3 (metal3)\
FP_IO_MODE-> 1 (Pins are equidistant)

Here you can see pins are equidistant

![](/day_2/pins_equadistant.PNG)

if `FP_IO_MODE = 2` (pins accumulate in one corner)

![](day_2/fp_io_mode_2.png)


Decoupling capacitors placed in floorplan 

![](/day_2/decapacitors_magic.PNG)

Tapcells

![](/day_2/tapcell.PNG)

We can find all standard cells obtained after synthesis are accumulated in one corner of main core.

![](/day_2/standard_cells.PNG)


Now to start placement type command\
`run_placement`

![](/day_2/placement_2.PNG)

While placement going on we come across two words:-HPWL and OVFL\
HPWL-Half perimeter wire length is just a model to approximate the wire lengths inorder to use it as parameter to optimise placement\
OVFL-This is overflow parameter which should decrease as placement optimization takes place\

![](/day_2/hpwl_opt.PNG)

Final placement pass 

![](/day_2/placement_pass.PNG)

Placement def file is stored in following `<\designs\picorv32a\runs\trail_run1\results\placement>`\
Copy SKY130A tech file and merged.lef to that folder where u find placement def\
Through terminal go to the directory of placement def file\
Type \
`magic -T sky130A lef read merged.lef def read picorv32.placement.def`\

Placement

![](/day_2/standard_cells_placement_1.PNG)

Placement at 0.8 target density (which i set using COMMAND `<set ::env(PL_TARGET_DENSITY) 0.8`>

![](/day_2/0.8_density.PNG)

Placement at 0.4 target density (which i set using COMMAND `<set ::env(PL_TARGET_DENSITY) 0.4`>

![](/day_2/0.4_targ_den.PNG)


## DAY-3

### TASK
To examine custom inverter cell layout of sky130 node in magic.To extract spice file and finally simulate in ngspice to find cell delay and transition times.

### NOTES



### LAB

Clone the repository shown below in figure

![](/day_3/vsd_clone.PNG)

In the repository downloaded we find a sky130_vsdinv.mag file.Open it in magic using sky130.tech technology file.\
`magic -tech sky130.tech sky130_vsdinv.mag`

![](/day_3/sky130_mag_label.PNG)


1->Nwell\
2->Metal-1(VDD)\
3->Locali\
4->locali and nsubstrate contact\
5->Metal-1 and locali contact\
6->Polysilicon\
7->Pdiffusion\
8->Metal-1(GND)\
9->Ndiffusion\
10->locali and Ndiffusion contact\
11->locali and Pdiffusion contact\
12->polysilicon and locali contact


![](/day_3/exttospice.PNG)

To extract spice file from the layout\
Type below commands as shown in figure in TCL/TK window of magic\
`extraxt all`-This commands extraxts the netlist in a different format as compared to spice netlist\
`exttospice`-In order to make extracted netlist compatible with spice we us this command\
`cthresh = 0` - Selects option to include all lumped capacitance greater than zero farad in netlist

![](/day_3/spice.PNG)

Files in vsd folder after extraction from magic.

![](/day_3/filesafter.PNG)

open the sky130_inv_parasetics.spice file in leafpad

![](/day_3/spice.PNG)



Here pshort.lib and nshort.lib are model files for mosfets of sky130 node.\
.trans command is inserted to indicate transient response\
VPWR ->DC source\
A-> pulse

![](/day_3/SPICE_SIMU.PNG)


if Cthresh=0 option not selected then u can see the extracted netlist will not have lumped capacitance included.

![](/day_3/noparaspice.PNG)


As part of exercise we needed to find cell fall delay and fall transition.

Cell fall delay-difference in time instances at which input reaches 50% of vdd from 0 and output reaches 50% of  vdd from vdd.\
Open the simulation file in ngspice
`ngspice sky130_inv_parasetics.spice`

![](/day_3/ngspice_1.PNG)

![](/day_3/cell_falldelay.PNG)

![](/day_3/cell_fall_delay_cal.PNG)

Cell fall delay=2.18ns - 2.15ns = 0.03ns

Fall transition=difference in time instances during falling of output from 80% of vdd to 20%vdd

![](/day_3/fall.PNG)

Fall transition 20% of vdd=0.65V

![](/day_3/fall_transition_20.PNG)

Fall trsnsition 80% of vdd=2.6V

![](/day_3/fall_transition_80.PNG)

Fall Transition=2.20ns - 2.16ns =0.04ns

![](/day_3/falltransitionlk.PNG)




## DAY-4




### TASK-4

To explore inverter layout in magic.Define the ports in magic and export lef file.Insert the custom lef file in our picorv32 design.Do static timing analysis and try to reduce resulting negative slack to zero by buffering and increasing the size of cells.Constructing clock tree using triton cts tool of openroad and do final sta analysis on modified synthesis file after clock tree synthesis.

### Notes


Input and output port should lie on the intersection of vertical and horizotal tracks\
The width of standard cell should be in odd multiples of horizontal track pitch\
The height of standard cell should be in odd multiples of verticle pitch\
Ensurung the port at horizonral and vertical track ensure that route can reach port\
cts done for max and min but should be done for typical as cts is typical\
we always want skew values to 10percent of timeperiod of clk\
inorder to meet skew one of the buffers are chosen ,starting from small size buffer\


### LAB

Pitches and origin of various metals in sky130 which can be found in track info file

![](/day_4/metal_pitch_textl.PNG)


To draw grid of origin and pitch of locali metal layer inorder to find whether the coustom inverter cell layout follows standard cell rules to be accuraletly placed by placement tool

![](/day_4/grid_li_com.PNG)


Here you can find the grid is intersecting with the ports perfectly which is indicating that standard cell ports are lie on intersection of vertical and horizontal tracks

![](/day_4/li_pitch_intersectinf.PNG)


Do Edit >> Text which opens up a dialogue box.Here you can define ports.

![](/day_4/Naming_ports.PNG)


Then go tcl/tk window of magic ,after selecting any port with the layer it is attached type what.There you can see everything like to what metal is connected and in what layer that port is present.Now the task is to define the class of the signal like inout,input or output and define function of the port like signal,ground or power.

![](/day_4/lef_proc.PNG)


Then extract lef file by `lef write` command

![](/day_4/lef_write_command.PNG)


LEF file

![](/day_4/lef_file.PNG)


Now go to files in vsdstdcelldesign folder ,here u can find lef file

![](/day_4/files_vsdcelldesign.PNG)


In `vsdstdcelldesign/libs` you can find three lib files.These lib files contains all information like power,delays and ports of all standard cells in particular format for three PVT corners(fast,slow and typical).

![](/day_4/library_files.PNG)


The view of fast lib file.Here the properties of our custum cell sky130_vsdinv can be found.Tools like yosis and opensta map our custom cell through this lib file only.

![](/day_4/sky130_inv_libcell.PNG)


Lookup tables are used inorder to characterize different properties like output delays,power,transition etc for different combinations of input slew and output capacitance.In below figure you can see characterization of rise transition and cell fall delay using 6x6 matrix where rows index represent input slew and column index represent output capacitance.STA tools use this information from lib file sor STA analysis.

![](/day_4/lookup_tables.PNG)


Now copy all the three lib files and lef file of our custom cell to src folder of picorv32a

![](/day_4/files_to_be_copied_tosrc.PNG)


Now in the config.ycl file of picorv32a specify loctaion of lib files and location of lef file as shown in figure.LIB_SYNTH variable is used by yosis to map sky130 pdk cells and LIB_TYPICAL,LIB_MAX and LIB_MIN are used by openSTA for static timing analysis.

![](/day_4/Mod_config_lib_lef.PNG)


After entering into openlane write two more commands as shown in figure below to include our custom lef file into merged.lef file.

![](/day_4/merge_inv_lef.PNG)


After running synthesis you can see our custom cell is included into synthesis file of picorv32a.

![](/day_4/sky130_vsd_in_abc.PNG)


STA hold time report of first synthesis run.We can see hold time is met.Hold slack time is positive.But it is too optimistic now as we hold time violations mostly can occur after clock tree synthesis.

![](/day_4/setup_slack_run1.PNG)


STA setup time report of first synthesis run.We can see setup slack is negative.Tns=1623 and wns=-15.96.A lot of modifications has to be done inorder to improve setup slack and bring it to zero.

![](/day_4/tns_wns_run_1.PNG)


We try to set SYNTH_STRATEGY variable to 1 which indicates that we want to prefer more faster response as compared to low area.So as result synthesis tool try to use more larger area cells or incoude buffers which has high drive strength but area and power increases.We set SYNTH_SIZING to 1 to indicate openlane to prefer sizing of cells as compared to including buffers.

![](/day_4/setting_synth_strategy_1_inc_area_decr_time.PNG)


After new slack run we can see tns and wns reduced.

![](/day_4/new_slack_run2.PNG)


Now we try to reduce MAX_FANOUT from 6 to 4.

![](/day_4/FAN_OUT_UPDATE_4.PNG)


After fanout change tns and wns further reduced.

![](/day_4/new_slack_fanout_run_3.PNG)


Now we try to open the picorv32a synthesis file in opensta software outside openlane do further chages.\
We need a **configuration file** **<sta.conf>** which states the library locations and some initial conditions.\
We need a **constraints file** **<my_base.sdc>** which is in sdc format followed by whole vlsi industry.\
We need the **library files** and **synthesis file**.\


**sta.conf** file

![](/day_4/setting_up_sta_conf_address.PNG)



**my_base.sdc** file.Here just we need to modify the clock port,set the driving cell and other design parameters.

![](/day_4/base.sdc.PNG)


Open sta.conf file in openSTA 

![](/day_4/sta.conf_command.PNG)


If we see the standard cells in critical path,we find that buffer of size 1 is driving a fanout of 4.

![](/day_4/BUF_1_fanout_4.PNG)


we find at net 13779 there is a buffer of size 1 driving four standard cells

![](/day_4/reprt_net_sta.PNG)


So we try to replace the buffer of size 1 at that net with buffer of size 4.It decreases the delay but increase the area and power consumption.Use the following command from image below.

![](/day_4/commands_replace_report.PNG)


Now tns and wns decreased from last run.

![](/day_4/after_replacing_one_buf.PNG)


We can find resultant cell is changed to buffer of size 4.

![](/day_4/result_buff_4_change.PNG)


Similarly after replacing two more buffers we can tns and wns again reduced.

![](/day_4/run_5.PNG)


Now write this new synthesis file after changing buffers to trial_run1 folder using the following command.

![](/day_4/write_verilog.PNG)


We can find chnaged buffers in picorv32a.synthesis file.

![](/day_4/finding_43204_cell_in_syntheis.PNG)


After this run_placement and check if placement is passed.

![](/day_4/placement_check_final_run5.PNG)


When you open picorv32a.placement.def file you can find the custom cell sky130_vsdinv

![](/day_4/sky130_vsdinv_in_placement.PNG)


Now we have do clock tree synthesis.Type  `run_cts`\
After a new netlist will be formed with clock buffers inserted to reduce clock skew.

![](/day_4/TRiton_netlist.PNG)


You can find new synthesis file with clock buffers inserted in synthesis folder.

![](/day_4/cts_synthesis.PNG)


After synthesis you can find def generated after cts in tmp folder

![](/day_4/current_def.PNG)


Now enter the openroad interface to do sta analysis on new synthesis file after cts.
type `openroad`.Here you need to gnerate .db files which requires lef and def files so use following commands.

`read lef /location/merged.lef` 

![](/day_4/reading_cts_lef.PNG)


`read def /location/picorv32a.cts.def`

![](/day_4/read_def_cts.PNG)


After doing writing and reading of .db files we need to read synthesis file after cts,library files and  my_base.SDC constraints as shown in image.Then generate report to check STA analysis of picorv32.cts.synthesis.v file.The required commands to type are highlighted in yellow colour.

![](/day_4/total_commands_cts.PNG)

Setup time report.Setup slack is negative.

![](/day_4/cts_setup_slack.PNG)

hold time report.Hold slack is negative.

![](/day_4/cts_hold_slack.PNG)

Here the problem is we used min and max libraries for sta analysis but tritoncts currently can only do clock tree syntesis for typical corner.So we need to change library to typical.So we need to exit openroad and do the previous steps again and now include typical library.

![](/day_4/typical_ts_sta.PNG)


Setup report for typical library.Setup slack is reduced comparatively.

![](/day_4/cts_typical_setup_slack.PNG)

Hold reprt for typical library.Hold slack is reduced comparatively

![](/day_4/typical_hold_cts_slack.PNG)

Tritoncts tool always tries to use smallest size clock buffer inorder to minimize the skew.So we need to remove clock buffer of size 1 from the collection of cloack buffers so that tritoncts choses immediate next smalles buffer of size 2.For this use follwing TCL command.

![](/day_4/CLK_1_BUF_REMOVED.PNG)

Also again before we are doing clock tree synthesis we need to set CURRENT_DEF variable to placement def file as it was modified to cts def file due to previous cts run.

![](/day_4/changing_current_def_to_placement.PNG)


Setup slack report.Setup slack is positive now.

![](/day_4/setup_slack_post_add_buf2.PNG)


Hold slack report.Hold slack is positive now

![](/day_4/hold_slack_post_add_buf2.PNG)


To check hold and setup clock skew,use below commands.

![](/day_4/setup_hold_skew.PNG)


Inorder to add clock buffer of size 1 again in clock buffers list use following tcl command.

![](/day_4/imp_comm.png)



## DAY-5

### TASK

To lay power distribution network and do the final routing using Fastroute for global routing nad tritonroute for detailed routing.

### LAB

To generate power distribution netwrok type `gen_pdn`

![](/day_5/gen_pdn.PNG)

The metal layers and pitch for each metal type used for power distribution

![](/day_5/Metal_layers.PNG)


Location of def file generated after power distribition .

![](/day_5/pdn_loc.PNG)

Power distribution view in magic.

![](/day_5/after_powerdn_not_including_merge.PNG)

Metal Layers description

![](/day_5/pdn_label.PNG)

1->metal 1(used to connect VDD nad ground of standard cells)
2->metal 4(this gets power or ground from metal 5 stripe and passes the power or ground to metal 1)
3->matal 5(this is the main strip which carries power and ground and distributes around the core to metal 4)
4->metal 4 and metal 1 contact
5->metal 5 and metal 4 contact



We always need to have standard cell with height of multiple of metal 1 pitch then only vdd and ground of each standard cell can be properly routed.

![](/day_5/Pitch_of_metal1_2.7.PNG)


Now do routing by using command `run_routing`
You can see tritonroute does several optimisations to get best possible routing

![](/day_5/7th_optimisation.PNG)

Routing pass

![](/day_5/completed_routing.PNG)

You can view the final route in magic

![](/day_5/Final_rout_1.PNG)

You can find custom inverter cell in final routing

![](day_5/Inkedinv_connections_LI.jpg)

Finally to get .gds file type `run_magic`


## ACKNOLEDGEMENT

**Kunal Ghosh**,Co-founder (VSD Corp. Pvt. Ltd)\
**Nickson Jose**,Teaching Assistant VSD\
**Praharsha**, Teaching Assistant VSD\
**Akurathi Radhika**,Teaching Assistant VSD\





























































 





























































