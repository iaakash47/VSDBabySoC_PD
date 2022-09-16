# VSDBabySoC_PD

VSDBabySoC is a small SoC including PLL, DAC, and a RISCV-based processor named RVMYTH


# Table of Contents
- [Introduction to VSDBabySoC](#introduction-to-vsdbabysoc)
  - [SoC](#soc)
  - [RVMYTH](#rvmyth)
  - [PLL](#pll)
  - [DAC](#dac)
- [VSDBabySoC Modeling](#vsdbabysoc-modeling)
- [RVMYTH Modeling](#rvmyth-modeling)
- [DAC Modeling](#dac-modeling)
- [PLL Modeling](pll-modeling)
- [Getting Started with VSDBabySoC](getting-started-with-vsdbabysoc)
- [IC Compiler II](ic-compiler-ii)
  - [ICC Design Planning Overview](icc-design-planning-overview)
     - [Design planning](#design-planning)
     - [Milkyway Reference Libraries](milkyway-reference-libraries)
     - [Technology File](#technology-file)
     - [Design Flow](#design-flow)
     - [Hierarchical Design Planning Flow](#hierarchical-design-planning-flow)
        - [Design Partitioning](#design-partitioning)
        - [Deciding on the Physical Partitions](#deciding-on-the-physical-partitions)
  - [Design Planning at Multiple Levels of Physical Hierarchy](#design-planning-at-multiple-levels-of-physical-hierarchy)
     - [Data Model](#data-model)
     - [Block Shaping](#block-shaping)
     - [Cell and Macro Placement](#cell-and-macro-placement)
     - [Power Planning](#power-planning)
     - [Pin Placement](#pin-planning)
     - [Timing Budgeting](#timing-budgeting)
  - [Design Validation During Design Planning](#design-validation-during-design-planning)
- [RVMYTH CORE](#rvmyth-core)
  - [Synthesis](#synthesis)
  - [Schematic](#schematic)
  - [Performing Physical Design](#performing-physical-design)
  - [Start gui](#start-gui)
  - [RVMYTH core in VSDBabySoC](#rvmyth-core-in-vsdbabysoc)
- [Outputs generated](#outputs-generated)
- [Violations and Future Work](#violations-and-future-work)
- [Acknowledgements](#acknowledgements)
- [Author](#author)

# Introduction to the VSDBabySoC

VSDBabySoC is a small yet powerful RISCV-based SoC. The main purpose of designing such a small SoC is to test three open-source IP cores together for the first time and calibrate the analog part of it. VSDBabySoC contains one RVMYTH microprocessor, an 8x-PLL to generate a stable clock, and a 10-bit DAC to communicate with other analog devices.

![vsdbabysoc_block_diagram](https://user-images.githubusercontent.com/88897605/190461280-a2a615cc-91fe-4a15-b21e-a5788a61de2f.png)

  
## SoC

An SoC is a single-die chip that has some different IP cores on it. These IPs could vary from microprocessors (completely digital) to 5G broadband modems (completely analog).A system on a chip, also known as an SoC, is essentially an integrated circuit or an IC that takes a single platform and integrates an entire electronic or computer system onto it. It is, exactly as its name suggests, an entire system on a single chip. The components that an SoC generally looks to incorporate within itself include a central processing unit, input and output ports, internal memory, as well as analog input and output blocks among other things. Depending on the kind of system that has been reduced to the size of a chip, it can perform a variety of functions including signal processing, wireless communication, artificial intelligence and more.

## RVMYTH

RVMYTH core is a simple RISCV-based CPU, introduced in a workshop by RedwoodEDA and VSD. During a 5-day workshop students (including middle-schoolers) managed to create a processor from scratch. 
The workshop used the TLV for faster development. All of the present and future contributions to the IP will be done by students and under open-source licenses.

## PLL

A phase-locked loop or PLL is a control system that generates an output signal whose phase is related to the phase of an input signal. PLLs are widely used for synchronization purposes, including 
clock generation and distribution.

## DAC

A digital-to-analog converter or DAC is a system that converts a digital signal into an analog signal. DACs are widely used in modern communication systems enabling the generation of digitally-defined transmission signals. As a result, high-speed DACs are used for mobile communications and ultra-high-speed DACs are employed in optical communications systems.

# VSDBabySoC Modeling

Here we are going to use IC Compiler II for running the complete Physical Design Flow of VSDBabySoC (consisting of RVMYTH : Digital block, PLL & DAC : Analog blocks). 

The results, conclusions and future work will be summarized accordingly. 

Some initial input signals will be fed into vsdbabysoc module that make the pll start generating the proper CLK for the circuit. The clock signal will make the rvmyth 
to execute instructions in its imem. As a result, the register r17 will be filled with some values cycle by cycle. These values are used by dac core to provide the 
final output signal named OUT. So we have 3 main elements (IP cores) and a wrapper as an SoC.

The complete Design flow is supposed to be run using SAED32_28nm PDKs, But due to some issues with the .lib files of SAED32_28nm, We had to proceed further/shift to Nangate45nm PDKs.
And due to some issues with the DAC and PLL files, for the time-being we had to go ahead with running only the RVMYTH Digital block, in the VSDBabySoC for learning purpose.


# RVMYTH Modeling

As we mentioned in [What is RVMYTH](#what-is-rvmyth) section, RVMYTH is designed and created by the TL-Verilog language. 

So we need a way for compile and trasform it to the Verilog language and use the result in our SoC. Here the `sandpiper-saas` could help us do the job.

[Here](https://github.com/iaakash47/vsdriscv.git) is the repo we used as a reference to model the RVMYTH


# DAC Modeling

For the synthesis of Digital to Analog Converter, that is designed using Design Compiler and verified using PrimeWave we will refer the below repository. 

[Here](https://github.com/iaakash47/avsddac28nm.git) is the repository that documents all the work done for DAC


# PLL Modeling

It is not possible to sythesis an analog design with Verilog, yet. But there is a chance to simulate it using `real` datatype.

We will use the following repository done by my teammate to model the PLL core:

[Here](https://github.com/ireneann713/PLL) is the repo we used as a reference to model the PLL

## VSDBabySoC Modeling

* Here we are going to model and simulate the VSDBabySoC using iverilog, then we will show the results using gtkwave tool. 
* Some initial input signals will be fed into vsdbabysoc module that make the pll start generating the proper CLK for the circuit.
* The clock signal will make the rvmyth to execute instructions in its imem
* These values are used by dac core to provide the final output signal named OUT


![VSDbabySoc_Pre_Synthesis](https://user-images.githubusercontent.com/88897605/184362846-9c4206c8-bf0d-40cd-bbb3-d97bd5830f9c.png)



In this picture we can see the following signals:

* CLK: This is the input CLK signal of the RVMYTH core. This signal comes from the PLL, originally.
* reset: This is the input reset signal of the RVMYTH core. This signal comes from an external source, originally.
* OUT: This is the output OUT signal of the VSDBabySoC module. This signal comes from the DAC (due to simulation restrictions it behaves like a digital     signal which is incorrect), originally.
* RV_TO_DAC[9:0]: This is the 10-bit output [9:0] OUT port of the RVMYTH core. This port comes from the RVMYTH register #17, originally.
* OUT: This is a real datatype wire which can simulate analog values. It is the output wire real OUT signal of the DAC module. This signal comes from the   DAC, originally.

**PLEASE NOTE** that the sythesis process does not support `real` variables, so we must use the simple `wire` datatype for the `\vsdbabysoc.OUT` instead. The `iverilog` simulator always behaves `wire` as a digital signal. As a result we can not see the analog output via `\vsdbabysoc.OUT` port and we need to use `\dac.OUT` (which is a `real` datatype) instead.

# Post-synthesis simulation

![VSDbabySoC_Post_Synthesis](https://user-images.githubusercontent.com/88897605/184362895-39e19c4c-c81a-44ea-b60c-a3fdd886a363.png)


In this picture we can see the following signals:

* \core.CLK: This is the input CLK signal of the RVMYTH core. This signal comes from the PLL, originally.
* reset: This is the input reset signal of the RVMYTH core. This signal comes from an external source, originally.
* OUT: This is the output OUT signal of the VSDBabySoC module. This signal comes from the DAC (due to simulation restrictions it behaves like a digital     signal which is incorrect), originally.
* \core.OUT[9:0]: This is the 10-bit output [9:0] OUT port of the RVMYTH core. This port comes from the RVMYTH register #17, originally.
* OUT: This is a real datatype wire which can simulate analog values. It is the output wire real OUT signal of the DAC module. This signal comes from the   DAC, originally.

**PLEASE NOTE** that the sythesis process does not support `real` variables, so we must use the simple `wire` datatype for the `\vsdbabysoc.OUT` instead. The `iverilog` simulator always behaves `wire` as a digital signal. As a result we can not see the analog output via `\vsdbabysoc.OUT` port and we need to use `\dac.OUT` (which is a `real` datatype) instead.

### VSDbabySoC Block

![VSDbabySoC_Block](https://user-images.githubusercontent.com/88897605/184362942-a7d9be30-077c-40ac-9003-dbdf9be5fd06.png)


### VSDbabySoC Schematic View

![Schematic_VSDbabySoC](https://user-images.githubusercontent.com/88897605/184362969-73f79bef-454b-4cc6-ac2b-cca60f558f29.png)


### RISC-V Core
RVMYTH core is a simple RISCV-based CPU
![core](https://user-images.githubusercontent.com/88897605/184493149-e5a3a646-0ceb-4249-8a2e-057860500c28.png)

![risc_v_core](https://user-images.githubusercontent.com/88897605/184363013-96ce58d3-3a15-4301-9cfd-c01166a09731.png)

# Design  Environment Constraints 
``` set_units -time ns
create_clock [get_pins {pll/CLK}] -name clk -period 10
set_max_area 8000;
set_max_fanout 5 vsdbabysoc;
set_max_transition 10 vsdbabysoc
#set_min_delay -max 10 -clock[get_clk myclk] [get_ports OUT]
set_max_delay 10 -from dac/OUT -to [get_ports OUT]


#set_input_delay[expr 0.34][all_inputs]



set_clock_latency -source 2 [get_clocks MYCLK];
set_clock_latency 1 [get_clocks MYCLK];
set_clock_uncertainty -setup 0.5 [get_clocks MYCLK];
set_clock_uncertainty -hold 0.5 [get_clocks MYCLK];

set_input_delay -max 4 -clock [get_clocks MYCLK] [get_ports VCO_IN];
set_input_delay -max 4 -clock [get_clocks MYCLK] [get_ports ENb_CP];
set_input_delay -min 1 -clock [get_clocks MYCLK] [get_ports VCO_IN];
set_input_delay -min 1 -clock [get_clocks MYCLK] [get_ports ENb_CP];

set_input_transition -max 0.4 [get_ports ENb_CP];
set_input_transition -max 0.4 [get_ports VCO_IN];
set_input_transition -min 0.1 [get_ports ENb_CP];
set_input_transition -min 0.1 [get_ports VCO_IN];





set_load -max 0.5 [get_ports OUT];
set_load -min 0.5 [get_ports OUT];
```
#### Timing Reports of VSDBabySoC
```
Startpoint: dac/OUT (internal path startpoint)
  Endpoint: OUT (output port)
  Path Group: default
  Path Type: max

  Des/Clust/Port     Wire Load Model       Library
  ------------------------------------------------
  vsdbabysoc_1       16000                 saed32hvt_tt0p85v25c

  Point                                    Incr       Path
  -----------------------------------------------------------
  input external delay                     0.00       0.00 r
  dac/OUT (avsddac)                        0.00       0.00 r
  OUT (out)                                0.01       0.01 r
  data arrival time                                   0.01

  max_delay                               10.00      10.00
  output external delay                    0.00      10.00
  data required time                                 10.00
  -----------------------------------------------------------
  data required time                                 10.00
  data arrival time                                  -0.01
  -----------------------------------------------------------
  slack (MET)                                         9.99


1
```
**Design Environment**: It Consist of Operating Conditions ,Wire Load Models and System Interface Requirements

**Operating Conditions**:It consist of process of voltage and temperature requirements

**Wire Load Model**:It allows Design Compiler to estimate the effect of the wire length and fan out on resistance.capacitance and area of nets,basically it used to calculate the wire delays 

#### Input and Ouput Timing
* In Design Compiler we need to specify the timing requirements for input and ouputs ports used to optimize the VSDbabySoC module 
* The **set_input_delay** and **set_output_delay** commands are used to constrained the input and output port delays
* The **set_input_delay** command is used to specify how much time is used by external logic or block
* The **set_output_delay** command is used to specify how much time is used by external logic or block needs for optimization
```
set_input_delay -max 4 -clock [get_clocks MYCLK] [get_ports VCO_IN];
set_input_delay -max 4 -clock [get_clocks MYCLK] [get_ports ENb_CP];
set_input_delay -min 1 -clock [get_clocks MYCLK] [get_ports VCO_IN];
set_input_delay -min 1 -clock [get_clocks MYCLK] [get_ports ENb_CP];

```
#### Combinational Delay
* In Design Compiler to constrain the Combinational path **set_max_delay** and **set_min_delay** commands are used to optimize the VSDbabySoC module 
* **set_max_delay** command allows to specify the maximum delay of a timing path
* **set_min_delay** command allows to specify the minimum delay of a timing path
```
#set_min_delay -max 10 -clock[get_clk myclk] [get_ports OUT]
set_max_delay 10 -from dac/OUT -to [get_ports OUT]
```
### Design Flow

![22](https://user-images.githubusercontent.com/88897605/190555704-8eda471b-48bf-4177-a185-ae32027f6c8d.PNG)

The physical design is the process of transforming a circuit description into the physical layout, which describes the position of cells and routes for the interconnections between them.
The main concern is the physical design of VLSI-chips is to find a layout with minimal area, further the total wire length has to be minimized. For some critical nets there are hard limitations for the maximal wire length.


### Hierarchical Design Planning Flow

The hierarchical design planning flow provides an efficient approach for managing large designs. By dividing the design into multiple blocks, different design teams can work 
on different blocks in parallel, from RTL through physical implementation. Working with smaller blocks and using multiply instantiated blocks can reduce overall runtime.
Consider using a hierarchical methodology in the following scenarios:

• The design is large, complex, and requires excessive computing resources to process the design in a flat form.

• You anticipate problems that might delay the delivery of some blocks and might cause the schedule to slip. A robust hierarchical methodology accommodates late design changes to individual blocks while maintaining minimal disruption to the design 
schedule.

• The design contains hard intellectual property (IP) macros such as RAMs, or the design was previously implemented and can be converted and reused.

After the initial design netlist is generated in Design Compiler topographical mode, you can use the hierarchical methodology for design planning in the IC Compiler II tool. Design 
planning is performed during the first stage of the hierarchical flow to partition the design into blocks, generate hierarchical physical design constraints, and allocate top-level timing 
budgets to lower-level physical blocks. The flow to implement a hierarchical design plan is shown in Figure below :

![hierarchical design flow](https://user-images.githubusercontent.com/83152452/190413869-a52aec73-5368-4b13-8660-158f465308bb.png)


#### Design Partitioning

After reading in the netlist and initializing the floorplan, you can determine the physical partitioning for your hierarchical design project. When deciding on the physical partitions, 
consider the following factors:

• Size
Partition your design with blocks of similar size. Small blocks should be grouped and large blocks should be divided when appropriate.

• Function
Partition your design using its functional units for verification and simulation purposes. Consider top-level connectivity and minimal block pin counts to avoid congestion and timing issues.

• Floorplan style
Different floorplan styles require different physical hierarchies to support them. An abutted floorplan style has no top-level logic and a channeled floorplan has either a small or large amount of top-level logic.

• Common hierarchy with Design Compiler topographical mode
To exchange SCANDEF information at the block level and the top level, the physical hierarchy used in Design Compiler topographical mode must also be used in the IC Compiler II tool.

#### Deciding on the Physical Partitions

The IC Compiler II tool provides the following features to help you decide on the physical 
partitions:

• Using the Hierarchy Browser
You can use the hierarchy browser to navigate through the design hierarchy, to examine the logic design hierarchy, and to display information about the hierarchical 
cells and logic blocks in your design. You can select the hierarchical cells, leaf cells, or other objects in layout or schematic views. The viewer provides a tree browser to help 
in understanding the design hierarchy, and information about the blocks in the design such as the utilization, number of standard cells, and so on.

• Committing Blocks
After you decide on an initial partitioning, you can commit the blocks. Blocks are committed early in the floorplanning flow, and abstract views are used to generate an 
initial floorplan and timing budgets for the design.


## Design Planning at Multiple Levels of Physical Hierarchy

Large, complex SoC designs require hierarchical layout methodologies capable of managing multiple levels of physical hierarchy at the same time. Many traditional design 
tools -- including physical planning, place and route, and other tools -- are limited to two levels of physical hierarchy: top and block. The IC Compiler II tool provides 
comprehensive support for designs with multiple levels of physical hierarchy, resulting in shorter time to results, better QoR, and higher productivity for physical design teams. Use 
the set_editability command to enable or disable specific blocks and design levels of hierarchy for planning.

![1](https://user-images.githubusercontent.com/83152452/190419795-77cbbdb9-d6a8-4678-a9c1-83d5fdb638f2.png)

The IC Compiler II tool provides support in several areas to accommodate designs with multiple levels of physical hierarchy:

### Data Model

The data model in the IC Compiler II tool has built-in support for multiple levels of physical hierarchy. Native physical hierarchy support provides significant advantages for multilevel physical hierarchy planning and implementation. 
When performing block shaping, placement, routing, timing, and other steps, the tool can quickly access the specific data relative to physical hierarchy needed to perform the function.

### Floor Planning

In a complex design with multiple levels of physical hierarchy, the block shaper needs to know the target area for each sub-chip, the aspect ratio constraints required by hard 
macro children, and any interconnect that exists at the sibling-to-sibling, parent-to-child, and child-to parent interfaces. For multi-voltage designs, the block shaper needs the target 
locations for voltage areas. These requirements add additional constraints for the shaper to manage. For multi-level physical hierarchy planning, block shaping constraints on lower 
level sub-chips must be propagated to the top level; these constraints take the form of block shaping constraints on parent sub-chips. To improve performance, the shaper does 
not need the full netlist content that exists within each sub-chip or block.

The IC Compiler II data model provides block shaping with the specific data required to accomplish these goals. For multi-voltage designs, the tool reads UPF and saves the 
power intent at the sub-chip level. The tool retrieves data from the data model to calculate targets based on natural design utilization or retrieves user-defined attributes that specify 
design targets.

![floorplan](https://user-images.githubusercontent.com/88897605/190554376-fab44ab0-4c66-4a5a-9e48-58ef5e459a5f.jpg)

* Flowplan Flowchart
![floorplanflow](https://user-images.githubusercontent.com/88897605/190554412-e9b825cd-1c7e-4678-a5d0-a92e4a3104fc.png)



### Cell and Macro Placement

After block shaping, the cell and macro placement function sees a global view of the interconnect paths and data flow at the physical hierarchy boundaries and connectivity 
to macro cells. With this information, the tool places macros for each sub-chip at each level of hierarchy. Because the tool understands the relative location requirements of 
interconnect paths at the boundaries at all levels, sufficient resources at the adjacent subchip edges are reserved to accommodate interconnect paths. The placer anticipates the 
needs of hierarchical pin placement and places macros where interconnect paths do not require significant buffering to drive signals across macros.

The placer models the external environment at the boundaries of both child and parent sub-chips by considering sub-chip shapes, locations, and the global macro placements. 
Using this information, the placer creates cell placement jobs for each sub-chip at each level of hierarchy. By delegating sub-chip placement across multiple processes, the tool 
minimizes turnaround time while maximizing the use of compute resources.


![macroplacement](https://user-images.githubusercontent.com/88897605/190554511-d8ef9d4d-7dba-4148-b69c-491453d4b714.jpg)
![macrospacing](https://user-images.githubusercontent.com/88897605/190554514-6ee22890-e3be-4855-89e6-6cf7d3110079.jpg)



### Power Planning

For power planning, the IC Compiler II tool provides an innovative pattern-based methodology. Patterns describing construction rules -- widths, layers, and pitches required 
to form rings and meshes -- are applied to different areas of the floorplan such as voltage areas, groups of macros, and so on. Strategies associate single patterns or multiple 
patterns with areas. Given these strategy definitions, the IC Compiler II tool characterizes the power plan and automatically generates definitions of strategies for sub-chips at 
all levels. A complete power plan is generated in a distributed manner. Because the characterized strategies are written in terms of objects at each sub-chip level, power plans 
can be easily re-created to accommodate floorplan changes at any level.

![powerplan](https://user-images.githubusercontent.com/88897605/190554190-bd01b503-7799-48b2-ab5e-2ad0ae931a7c.png)



### Pin Placement

With block shapes formed, macros placed, and power routed, pin placement retrieves interface data from all levels and invokes the global router to determine the optimal 
location to place hierarchical pins. The global router recognizes physical boundaries at all levels to ensure efficient use of resources at hierarchical pin interfaces. Pins are aligned 
across multiple levels when possible. Like all IC Compiler II operations, the global router comprehends multiply instantiated blocks (MIBs) and creates routes compliant with each 
MIB instantiation. To place pins for MIBs, the pin placement algorithm determines the best pin placement that works for all instances, ensuring that the pin placement on each 
instance is identical. Additionally, pin placement creates feedthroughs for all sub-chips, including MIBs, throughout the hierarchy. The global router creates feedthroughs across 
MIBs, determines feedthrough reuse, and connects unused feedthroughs to power or ground as required.

### Timing Budgeting

The IC Compiler II tool estimates the timing at hierarchical interfaces and creates timing budgets for sub-chips. The timing budgeter in IC Compiler II creates timing constraints for
all child interface pins within the full chip, the parent and child interfaces for mid-level subchips and the primary pins at lowest level sub-chips. The entire design can proceed with 
placement and optimization concurrently and in a distributed manner.

To examine critical timing paths in the layout or perform other design planning tasks, you can interactively view, analyze, and manually edit any level of the design in a fullchip context. You can choose to view top-level only or multiple levels of hierarchy. 
When viewing multiple levels, interactive routing is performed as if the design is flat. At completion, routes are pushed into children and hierarchical pins are automatically added.


## Design Validation During Design Planning

As you proceed through the design planning flow, you can use the check_design command to validate your design and ensure that it is ready for the next design stage. 
The command performs one or more checks on your design, based on the arguments you specify. To use the check_design command to check your design, specify the command 
and the -checks option followed by one or more check keywords.

The following example performs the dp_pre_floorplan checks:
```
icc2_shell> check_design -checks {dp_pre_floorplan}
```

The list of keywords related to design planning for the -checks option is shown in the following list.

```
• dp_pre_floorplan
◦ Checks that the technology file information is correct
◦ Checks that the layer directions are set
◦ Checks that the design contains both horizontal and vertical layers
```

```
• dp_pre_create_placement_abstract
◦ Checks that the constraint mapping file specifies a UPF file for all blocks
◦ Checks that the Verilog files are in place for the outline view for blocks
```

```
• dp_pre_block_shaping
◦ Checks that the target utilization or area exists for all black boxes
◦ Checks that there is at least one block or voltage area to shape
◦ Checks that the core area and block boundaries are valid
◦ Checks that the block grid is set if the design contains multiply instantiated blocks (MIBs)
◦ Performs the multivoltage checks related to the shape_blocks command
```

Figure : Fast interactive analysis through multiple-levels of physical hierarchy and MIB

![standardcell](https://user-images.githubusercontent.com/88897605/190555175-0e6b1768-c817-432d-a321-52bd182abcdf.jpg)


```
• dp_pre_macro_placement
◦ Checks that block shapes do not overlap
◦ Checks that blocks and voltage areas are inside the parent boundaries
◦ Performs basic placement constraint checking, such as overlapping or out-of bounds relative locations constraints, macro cell width and height that are an even multiple of the FinFET grid, and macro cell orientation
◦ Checks that each block has a logical hierarchy
◦ Checks block, voltage area, exclusive movebound, and hard movebound utilization
◦ Performs the multivoltage checks related to the create_placement command
```

```
• dp_pre_power_insertion
◦ Checks that the preferred routing direction is set for the routing layers
◦ Checks that the tracks exist for routing layers
```

Figure : Intelligent and accurate analysis for congestion and power

![power1](https://user-images.githubusercontent.com/88897605/190555296-78f61371-56ba-495b-b462-eff78c53d760.jpeg)


```
• dp_pre_pin_placement
◦ Performs pin constraint checks, including topological constraints, individual constraints, constraints propagated from individual pins, bundle pin constraints, block pin constraints, size-related constraints, pin guide and pin blockage constraints, routing guide constraints, and routing blockage constraints
◦ Checks that the routing direction of each layer in a topological constraint is consistent with the block edge constraints
◦ Checks for consistent edge directions in topological constraint pairs
◦ Checks for off-edge pins that are part of a feedthrough constraint
◦ Checks for possible overlaps with existing internal block objects such as route blockages, pin blockages, fixed pins, preroutes, and so on
◦ Runs the checks performed by the check_mib_for_pin_placement command
```

Figure : Sub Block Configuration

![subblock](https://user-images.githubusercontent.com/88897605/190555403-fccedb05-2dda-41fc-8842-318b86942abe.jpeg)


```
• dp_pre_push_down
◦ Runs the checks performed by the check_mib_alignment command
```

```
• dp_pre_create_timing_abstract
◦ Checks that the constraint mapping file specifies SDC files for all blocks
```

```
• dp_pre_timing_estimation (timing abstract checks)
◦ Checks that the top and block levels have defined modes and corners
◦ Checks that the top-level modes and corners have corresponding block-level modes and corners
◦ Checks that the top level contains at least one clock, at least one scenario using a nonestimated corner, and the corner has parasitic parameters
```

```
• dp_pre_timing_estimation (placement abstract checks)
◦ Performs the pre_create_timing_abstract checks
◦ Checks whether placement abstracts exist
```

```
• dp_pre_budgeting
◦ Checks that the estimated_corner is available
◦ Performs the same checks as dp_pre_timing_estimation
```

```
• dp_floorplan_rules
◦ Checks the segment parity rule
◦ Checks the macro spacing rule
◦ Runs the checks performed by the check_finfet_grid command
```


# Getting Started with VSDBabySoC

1. Exporting the LEF Files for the Analog Blocks, i.e, DAC and PLL:

Library Exchange Format [LEF]
- Cell Abstract View/The Physical Library
- Std Cell LEF
- Macro LEF
- I/O LEF

LEF contains 
- Cell name, Shape, Size, Orientation, Class
- Port/Pin Name, Direction and Layout Geometries
- Obstruction/Blockages 
- Antenna Diff Area
```
Custom Compiler (CC) Home Page > Export > LEF & Export > Stream
```
#### RVMYTH using 45nm Technology Node
![Screenshot from 2022-09-15 23-25-08](https://user-images.githubusercontent.com/88897605/190477275-6391df34-cf34-4560-8e91-4c0f4a679f35.png)
![Screenshot from 2022-09-15 23-17-19](https://user-images.githubusercontent.com/88897605/190477345-9f97acf3-b366-48b1-8135-453642516254.png)
![Screenshot from 2022-09-15 23-17-32](https://user-images.githubusercontent.com/88897605/190477460-3198f7c9-a2cb-49d1-ba51-d0c73627b04c.png)
![Screenshot from 2022-09-15 23-17-58](https://user-images.githubusercontent.com/88897605/190477521-24964735-a182-4384-a36b-b69cfdda6dff.png)
![Screenshot from 2022-09-15 23-20-47](https://user-images.githubusercontent.com/88897605/190477581-cb668531-3433-479d-a76c-24aed6229750.png)


2. For the Digital Block, i.e, RVMYTH:

```
Use the rvmyth.v file
```

3. Write a vsdbabysoc.v code with modules named, rvmyth
```
module vsdbabysoc_1 ( OUT, reset, clk );
  input reset, clk;
  output  [9:0] OUT;
  rvmyth core ( .OUT(OUT), .CLK(clk), .reset(reset) );
endmodule
```
Here is the vsdbabysoc.tcl (Or, whichever file name you give [filename.tcl] ) script file for generating the Gate-Level Netlist as ```vsdbabysoc_gtlvl.v``` file.

### Synthesis
Synthesis is responsible for converting the RTL description into a structural gate level based netlist. This netlist instantiates every element (standard cells and macros) that compose the circuit and its connections.

Synthesis can be described as follows:
```
Synthesis = Translation + Optimization + Mapping
Synopsys Design Compiler is the tool used to perform a logical synthesis. Its inputs are:
-The RTL description – Verilog or VHDL.
-The GTECH library – General technology library. Not tied to any specific technology(gates, flip flops)
-DesignWare Library –Synthetic library (adders, multipliers, comparators, etc).
-The standard cell library – the specific target library
-The defined constraints – synthesis goals regarding timing, area, capacitance, max transition, fanout.
-Design Environment: The operating conditions (Libraries corners), wire load models.
First, DC reads the RTL description to its memory and translates it into an unmapped GTECH netlist. Then, considering the design constraints and design environment, DC compiles the GTECH netlist into the target library cells and optimizations are made to meet the design constraints. For this phase, all clock, sets and resets signals should be marked as ideal, since synthesis is a process with limitations regarding physical characteristics. Finally a set of reports are written and a gate level netlist is exported to be used by the place and route tool.Shows the functional flow of synthesis using Synopsys Design Compiler.
```
### Synthesis Verification

The first step is to verify a set of reports, which have information about timing, area, fanout and shows the violations to the defined constraints.These reports must be interpreted to check if there are violations (setup time, hold times,area, max transition, etc.).In case of violations DC can try to fix them by running optimization algorithms. If DC cannot fix the violations, one must go back to RTL coding. With these reports it is possible to check if the design is synthesizable and, therefore, if it is possible to proceed.The final verification before proceeding to Place&Route is to run Formality, which is a logical verification tool. It takes the final netlist generated by DC and checks the logical equivalence with the RTL description.

### Place&Route

Place&Route is the backend stage that converts the gate level netlist produced during synthesis into a physical design. Although the name denotes for two phases, the Place&Route stage can be divided in three steps: Placement, Clock Tree Synthesis (CTS) and Routing.Placement involves placing all macros and cells into a certain and predefined space. It is done in two phases. The first one, called Coarse Placement, places the standard cells in order to optimize timing and/or congestion but not taking in account overlapping prevention. The second phase, which is named Legalize, eliminates overlap problems by placing the overlapping cells in the closest available space.Clock tree synthesis is the creation of a balanced buffer tree in all high fanout clock nets to avoid violations regarding clock skew, max transition time, capacitance and setup and hold times.

Routing is responsible for designing all the wires needed to connect all cells of the circuit, while following the rules of the manufacture process. The connections between cells are done using metal layers placed one over the other and connected through vias. Routing has a negative impact on timing, transition and capacitance slacks. It introduces RC parasitic effects that cause delay, signal noise and increase IR drop. To minimize the parasitic impact, clock signals should be routed first and in middle metal layers, away from the noisy power supplies of the standard cells.

Routing is done in three phases: Global Routing (design routing nets), Track Assignment (assign nets to specific metal layers), and Search&Repair (fix violations).Using Synopsys IC Compiler the design is, first, placed, followed by the clock tree synthesis(CTS) and, finally the routing of every cell. The result is a post-layout netlist and a GDS II file.
![placement](https://user-images.githubusercontent.com/88897605/190554640-d43e438c-3b8f-4667-bd00-81d803042de4.jpeg)

* Placement Flowchart
![placementsteps](https://user-images.githubusercontent.com/88897605/190554642-3521dcc1-8787-4dda-ba3e-08baf152f951.jpeg)

* Placement Design Flow
![placementsteps1](https://user-images.githubusercontent.com/88897605/190554645-3ca98eaa-40aa-48ee-8521-0eaf9cc8ad3c.jpeg)

* Placed Cells
![placment](https://user-images.githubusercontent.com/88897605/190554646-6fe12b7d-8d89-4abd-a70b-2cbeab6014fc.png)


### The steps taken to perform Place&Route using ICC are as follows:

Placement
1 - Create one empty milkyway library.
This library is linked to the technology file (physical rules) and the standard cells library (cells size, shape and pin location).
```
create_mw_lib ${design}_lib –technology $tech_file –open –mw_reference_library
$mw_ref_lib
```

2 -Load Synthesis netlist
It is the netlist created by Design Compiler. It will be linked to the previously loaded physical and standard cells library.
```
read_verilog $verilog_file   (read_verilog vsdbabysoc.v)
```

3 – Connect the standard cells power pins to the design power supplies.
pg_connect $pwr_net $gnd_net $pwr_pin $gnd_pin

3 – Load TLU+ files
TLU+ files are provided by the foundry and give important information about the parasitic effects between cells and nets. This information will be used to correctly place and route all cells.
```
load_tlup $tlup_cworst $tlup_cbest $tlup_map
```
4 – Load the floorplan
The floorplan is the initial physical shape of the circuit. It has information about the circuit boundaries, the I/O pin location, the places where standard cells cannot be placed and the upper metal power straps. These straps are done in upper metal in order to have less resistance and smaller IR drop. The floorplan is previously done using Synopsys Custom Designer that produces the files to be loaded by ICC.
```
source vsdbabysoc.tcl
```
5 – Load the design constraints
Placement and routing are done in order not to violate these constraints.
```
set mode <functional_mode_name>
remove_sdc

source ${design}_constraints.tcl
set_library
```
6 – Check for special design constraints
Some standard cells libraries demand the use of special cells:
-Tap cells 
– Polarization cell that are added by rows.
-End cap cells 
– Placed for nwell continuity. Added in several ways like the beginning or
end of each row.
```
7 – Antenna Issues
Antenna issues appear with the existence of huge single metal nets that accumulate a big amount of charge and can damage the connected transistors. As a solution diodes can be placed in all input and output ports to discharge to VSS. As an alternative a metal bridge can be
added in an upper metal
``` 
insert_diodes $diode_cell
```
8 – Load ICC internal settings
This is done to improve the timing correlation between ICC and PrimeTime, enable ICC to check for antenna issues during routing and to load other constraints.
```
source settings.place.tcl
```

9 – Perform the coarse placement
Place the cells inside the floorplan. They will be placed in order to meet timing but not avoiding overlapping cells.
```
create_placement –effort high –timing_driven –congestion –congestion_effort high
```

10 – Legalize the placement
Legalize is the process of eliminating overlap problems by placing overlap cells in the closest
available space.
```
legalize_placement –effort high –incremental -timing
```
11 – Reorder the scan chain
In designs including scan chains, this step replaces the Q to SI (Serial Input) connections from too far way flip flops to closer ones.
```
read_def ${design}.scandef
optimize_dft
check_scan_chain
report_scan_chain```


### Parasitic extraction
Parasitic extraction has the objective to create an accurate RC model of the circuit so that future simulations and timing, power and IR Drop analyses can emulate the real circuit response. Only with this information, all the analyses and simulations can report results close
to the real functioning of the circuit. This way this stage needs to precede all signoff analyses.
Star RC is the Synopsys tool capable of performing parasitic extraction. It takes the postlayout Milkyway database and the NXTGRD files provided by the foundry (cells parasitic information) and produces SPEF (Standard Parasitic Exchange Format) and SBPF (Synopsys Binary Parasitic Format) files.

### Static Timing Analysis (STA)
STA is a method to obtain accurate timing information without the need to simulate thecircuit. It allows detecting setup and hold times violations, as well as skew and slow paths that limit the operation frequency.Synopsys PrimeTime allows running STA over a physical design, for each corner. Taking as inputs the post-layout netlist and parasitic and standard cells information it outputs a series of
reports, which give the possibility to detect timing violations. As mentioned before these timing violations can be fixed by inserting buffers or resizing cells. With PrimeTime it is possible to identify where to perform these modifications and test them. When a list of new buffers and resized cells is available, the modifications need to be done back in ICC, followed by another parasitic extraction and STA to check the results. This process is done iteratively until no violations are reported.

![staio](https://user-images.githubusercontent.com/88897605/190554896-a6aab4a0-e116-4b86-a329-a4d9ebbe8128.jpeg)


### Post-layout Verification
Once again, formality should be run to check the logical equivalence of the post-layout netlist with the RTL description.The huge number of transistors in a circuit can make the voltage level drop below a defined margin that ensures that the circuit works properly. IR Drop analysis allows checking the power grid to ensure that it is strong enough to hold that minimum voltage level. Synopsys PrimeRail
is the tool that outputs IR-drop and EM analyses reports.
Then, PrimeTime-PX is the tool responsible of performing power analyses to estimate the power consumption of the circuit, for each corner. It has the capability of computing the dynamic and static power consumption of the whole design or the power consumption of each
cell or macro.The final step is to run Synopsys Hercules which is a DRC/LVS verification tool.
DRC (Design Rules Checking) checks if the foundry geometric and connectivity rules are met.
Examples of DRC´s include: Metal to metal spacing; well to well spacing; minimum metal
width; Antenna Effect; Metal fill density.
LVS (Layout Versus Schematic) checks if the physical circuit corresponds to the original circuit
schematic.

```
set target_library [list /home/skandha/KUNAL/icc2_workshop_collaterals/nangate_typical.db]
set link_library [list /home/skandha/KUNAL/icc2_workshop_collaterals/nangate_typical.db] 
set symbol_library ""

read_file -format verilog {/home/aakash/rvmyth_45nm/src/module/vsdbabysoc.v}
read_verilog /home/aakash/rvmyth_45nm/src/module/rvmyth.v
read_verilog /home/aakash/rvmyth_45nm/src/module/clk_gate.v

read_lib -lib /home/aakash/rvmyth_45nm/src/module/nangate45nm.lib
read_lib -lib /home/aakash/rvmyth_45nm/src/module/nangate_typical.lib

analyze -library WORK -format verilog {/home/aakash/rvmyth_45nm/src/module/vsdbabysoc.v}
elaborate vsdbabysoc -architecture verilog -library WORK
analyze {}

set_units -time ns
create_clock -name MYCLK -per 10 [get_ports CLK];
#create_clock [get_pins CLK] -name MYCLK -period 10
set_max_area 8000;
set_max_fanout 5 vsdbabysoc;
set_max_transition 10 vsdbabysoc
#set_min_delay -max 10 -clock[get_clk myclk] [get_ports OUT]
#set_max_delay 10 -from dac/OUT -to [get_ports OUT]
#set_input_delay[expr 0.34][all_inputs]
#set_clock_latency -source 2 [get_clocks MYCLK];
#set_clock_latency 1 [get_clocks MYCLK];
set_clock_uncertainty -setup 0.5 [get_clocks MYCLK];
set_clock_uncertainty -hold 0.1 [get_clocks MYCLK];
set_input_delay -max 5 -clock [get_clocks MYCLK] [get_ports reset];
set_input_delay -max 5 -clock [get_clocks MYCLK] [get_ports CLK];
set_input_delay -min 1 -clock [get_clocks MYCLK] [get_ports reset];
set_input_delay -min 1 -clock [get_clocks MYCLK] [get_ports CLK];
set_input_transition -max 0.4 [get_ports reset];
set_input_transition -max 0.4 [get_ports CLK];
set_input_transition -min 0.1 [get_ports reset];
set_input_transition -min 0.1 [get_ports CLK];
set_load -max 0.5 [get_ports OUT];
set_load -min 0.5 [get_ports OUT];

check_design

compile_ultra

file mkdir report
write -hierarchy -format verilog -output /home/aakash/rvmyth_45nm/src/module/report/vsdbabysoc_gtlvl.v
write_sdc -nosplit -version 2.0 /home/aakash/rvmyth_45nm/src/module/report/vsdbabysoc.sdc
report_area -hierarchy > /home/aakash/rvmyth_45nm/src/module/report/vsdbabysoc.area
report_timing > /home/aakash/rvmyth_45nm/src/module/report/vsdbabysoc.timing
report_power -hierarchy > /home/aakash/rvmyth_45nm/src/module/report/vsdbabysoc.power

gui_start
```

4. For the Gate-Level Synthesis of vsdbabysoc.v

```
Design Compiler (DC) shell [dc_shell] > source vsdbabysoc.tcl > generates the vsdbabysoc_gtlvl.v file (Synthesis file)
```

NOTE : You can check the contents of vsdbabysoc.tcl in the files provided in repository.

5. Perform the Physical Design:

```
Go to IC Compiler II Shell [icc2_shell] > source top.tcl > Performs the complete Physical Design Flow (The complete flow will be explained in the repository) > Note the results
```

NOTE : You can check the contents of top.tcl in the files provided in repository.

# IC Compiler II

![icc2](https://user-images.githubusercontent.com/88897605/190468150-ede9d568-7aac-4b5d-b55f-23dde4f1cfc8.png)


IC Compiler II is the industry leading place and route solution that delivers best-in-class quality-of-results (QoR) for next-generation designs across all market verticals and process technologies while enabling unprecedented productivity. IC Compiler II includes innovative for flat and hierarchical design planning, early design exploration, congestion aware placement and optimization, clock tree synthesis, advanced node routing convergence, manufacturing compliance, and signoff closure.

![icc2_1 png](https://user-images.githubusercontent.com/88897605/190468514-47d9a6e4-4feb-4246-b4ee-031f43132ebe.png)

IC Compiler II is specifically architected to address aggressive performance, power, area (PPA), and time-to-market pressures of leading-edge designs. Key technologies include a pervasively parallel optimization framework, multi-objective global placement, routing driven placement optimization, full flow Arc based concurrent clock and data optimization, total power optimization, multi-pattern and FinFET aware flow and machine learning (ML) driven optimization for fast and predictive design closure. Advanced Fusion technologies offer signoff IR drop driven optimization, PrimeTime delay calculation within IC Compiler II, exhaustive path-based analysis (PBA) and signoff ECO within place 
and route for unmatched QoR and design convergence.


Once the ```top.tcl``` file is run, type ```start_gui``` in ```icc2_shell``` and you will observe the following:
![final_chip](https://user-images.githubusercontent.com/88897605/190481427-bdcb554c-74ad-4da0-9a9e-347f83f3cc25.png)

The FINCELLs are shown below:
![5](https://user-images.githubusercontent.com/88897605/190481954-a02d88c6-dab8-475b-b40e-e5cf49fde3a1.png)
![6](https://user-images.githubusercontent.com/88897605/190481964-2a2a83c9-e26c-4d0b-b84a-edec8b64497d.png)

## RVMYTH core in VSDBabySoC

Here are the pictures showing the standard cells placed :

![2](https://user-images.githubusercontent.com/88897605/190488164-17eb0e3c-8c8c-48b9-9333-fe5dc5c28bae.png)
![7](https://user-images.githubusercontent.com/88897605/190488176-1d478dff-3d22-443c-bf89-9994962f6163.png)
![10](https://user-images.githubusercontent.com/88897605/190488181-cc3b8bab-914c-428f-9dfb-d1228383d590.png)
![11](https://user-images.githubusercontent.com/88897605/190488186-aff7759f-7c4f-40c3-be6e-e0ff53311ac3.png)

# Outputs generated

1.Type ``` set_propagated_clock [all_clocks] ``` in ```icc2_shell``` as shown below:

![Screenshot from 2022-09-16 00-40-23](https://user-images.githubusercontent.com/88897605/190489332-48e3c197-3136-42c7-9a09-6c73f69ee403.png)

2.Type ``` estimate_timing ``` in ```icc2_shell``` and the report generated is shown below:

```
 Startpoint: reset (input port clocked by MYCLK)
  Endpoint: core/CPU_reset_a1_reg (rising edge-triggered flip-flop clocked by MYCLK)
  Mode: func1
  Corner: estimated_corner
  Scenario: func1::estimated_corner
  Path Group: **in2reg_default**
  Path Type: max

  Point                                            Incr      Path       Delta Incr     Analysis
  ----------------------------------------------------------------------------------------------------
  clock MYCLK (rise edge)                          0.00      0.00
  clock network delay (ideal)                      0.00      0.00
  input external delay                             5.00      5.00 f
  reset (in)                                       0.00      5.00 f      0.00
  core/CPU_reset_a1_reg/D (DFF_X1)                 0.01 e    5.01 f      0.00
  data arrival time                                          5.01        0.00        Delta arrival

  clock MYCLK (rise edge)                         10.00     10.00
  clock network delay (ideal)                      0.00     10.00
  core/CPU_reset_a1_reg/CK (DFF_X1)                0.00     10.00 r      0.00
  clock uncertainty                               -0.50      9.50
  library setup time                              -0.04      9.46
  data required time                                         9.46
  ----------------------------------------------------------------------------------------------------
  data required time                                         9.46
  data arrival time                                         -5.01
  ----------------------------------------------------------------------------------------------------
  slack (MET)                                                4.45


1
```

# Violations and Future Work

1. Type the following command in ```icc2_shell``` once the layout is generated:

``` report_constraints -all_violators -nosplit -verbose -significant_digits 4 > violators.rpt ```

![Screenshot from 2022-09-16 00-47-43](https://user-images.githubusercontent.com/88897605/190491066-4f159a61-f348-48c4-9584-dfc42223433d.png)

2. To view the reports type:

``` vim violators.rpt ```
![Screenshot from 2022-09-16 00-49-43](https://user-images.githubusercontent.com/88897605/190491215-947ffd6a-fdc6-4d90-b41e-d01f19f7ca38.png)

One such violation is shown below:
```
Startpoint: core/CPU_imm_a2_reg[23] (rising edge-triggered flip-flop clocked by MYCLK)
  Endpoint: core/CPU_imm_a3_reg[23] (rising edge-triggered flip-flop clocked by MYCLK)
  Mode: func1
  Corner: func1
  Scenario: func1
  Path Group: MYCLK
  Path Type: min

  Point                                            Incr       Path  
  --------------------------------------------------------------------------
  clock MYCLK (rise edge)                          0.0000    0.0000
  clock network delay (propagated)                 0.1750    0.1750

  core/CPU_imm_a2_reg[23]/CK (DFF_X1)              0.0000    0.1750 r
  core/CPU_imm_a2_reg[23]/Q (DFF_X1)               0.1257    0.3007 r
  core/CPU_imm_a3_reg[23]/D (DFF_X1)               0.0055    0.3061 r
  data arrival time                                          0.3061

  clock MYCLK (rise edge)                          0.0000    0.0000
  clock network delay (propagated)                 0.2061    0.2061
  core/CPU_imm_a3_reg[23]/CK (DFF_X1)              0.0000    0.2061 r
  clock uncertainty                                0.1000    0.3061
  library hold time                                0.0283    0.3344
  data required time                                         0.3344
  --------------------------------------------------------------------------
  data required time                                         0.3344
  data arrival time                                         -0.3061
  --------------------------------------------------------------------------
  slack (VIOLATED)                                          -0.0282
```  
  
* Here the setup time is met but the hold time is not met and is violated. 
* NOTE : The hold time has to be met if the IP has to go for tapeout or physical implementation.


3. Here are some of the other violations snapshots shown below:

This path shows that the regular buffer in clock path due to inavailability of clock buffer in PDK
4. Future work would be to integrate the RVMYTH Digital block to PLL and DAC Analog blocks using the LEF Files.

5. The above mentioned violations in point number 1,2 and 3 has be corrected before going for Physical Implementation.


# Acknowledgements
- [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.
- [VSDBabySoC](https://github.com/manili/VSDBabySoC.git).
- Skandha, T.A.
# Contributor
Aakash.K</br>
Contact:iaakashkrish@gmail.com</br>

