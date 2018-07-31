# TreeNoC

This repository provides the source code for binary tree based network on chip as well as asynchronous binary tree based network on chip.
There are two folders

## Directory structure 

### SyncBTree 

Directory for synchronous binary tree. Synchronous binary trees run using a single clock signal. 
There 4 directories namely BTree4PE, BTree8PE, BTree16PE and BTree32PE each containing NoCs with sizes varying from 4 to 32 PEs.
The design is quite scalable and users can build larger networks (such as with 64 or 128 switches).
Each folder has a Vivado project (project_1.xpr), which is already configured with all the required files for simulation and synthesis.
Following are the files inside the directory

**BTree.v**: Top most RTL file for synthesis 

**switch1.v** : Synchronous switch used with complete cross bar. Used at the lowest level of the tree. PEs are directly linked with these switches.

**switch2.v** : Synchronous switch used with partial cross bar. Used with intermediate levels of the tree.

**HLeaf.v**: An H leaf contain two type-1 switches and a type-2 switch

**CentreH.v** : Binary trees are placed and routed in the form of H-trees. A center H connects maximum 4 H leaves.

**pe.v** : PE model used for simulation

**tb.v** : Top most file for simulation

#### <span style="color:blue">Simulation</span>

In vivado users can directly simulate the tb module. If users are using any other simulators (such as iVerilog), they can add all the above mentioned files to the simulator 
and choose tb as top module. Users will also need to add Xilinx IP library to third party simulators since the designs uses Xilinx FIFO cores.
At the end of simulation, the simulator displays the packet received by each PE and the overall throughput of the NoC (number of packets received per clock cycle).
The simulator also generates a "receive_log.csv" file which lists the PE address, injection time and latency.
In case of Vivado, the log file will be located inside the project_1/sim folder. 

### ASyncBTree

Directory for asynchronous binary tree. Asynchronous binary trees run with different clock signals at different tree levels.
As we move higher in the tree level, higher should be the clock frequency. By doing so the over all throughput of the network can be improved.
The files and simulation steps are similar to that of SyncBTree.
But the functionality of switches are slightly different.

**HNoC.v**: Top most RTL file for synthesis 
 
**switch1.v** : Asynchronous switch used with complete cross bar. Interface to PE runs at a clock frequency and the interface to the parent node runs at a higher frequency.

**switch2.v** : Similar to switch-1 but having only a partial cross bar.

**SwitchSingleClock.v** : It is a synchronous switch with partial cross bar. Used in intermediate levels of the tree.

#### <span style="color:blue">Implementation</span>

To find resource utilization, NoCs with size 4 can be directly synthesized.
For larger ones, we will have to use the "out of context" mode of Vivado.
The following commands can be typed into the Vivado TCL interface or used as a tcl script.

synth_design -mode out_of_context -top BTree  
opt_design  
place_design  
route_design  
report_utilization  
report_timing_summary  

The timing constraint file is already added to the project. 