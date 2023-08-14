This repository summarizes the day to day progress made during the VSD Tapout program -

[Day 0 - Installation of the required tools](#day0---installation-of-the-required-tools)  
[Day 1 - Introduction to Synthesis using Yosys](#day1---introduction-to-synthesis-using-yosys)  
[Day 2 - Hierarchical and Flat Synthesis ](#day2---hierarchical-and-flat-synthesis)   
[Day 3](#day3---abc)
## Day0 - Installation of the required tools  
<details>
 <summary>
Yosys
 </summary>
I installed Yosys using following commands :  

```
git clone https://github.com/YosysHQ/yosys.git
$ cd yosys-master   
$ sudo apt install make (If make is not installed please install it)   
$ sudo apt-get install build-essential clang bison flex \  
    libreadline-dev gawk tcl-dev libffi-dev git \  
    graphviz xdot pkg-config python3 libboost-system-dev \  
    libboost-python-dev libboost-filesystem-dev zlib1g-dev  
$ make config-gcc  
$ make   
$ sudo make install
```  
Below is the screenshot of successful launch  
![Screenshot from 2023-07-31 09-44-43](https://github.com/Rachanaka/iiitb-asic/assets/140998470/2c6eaf8d-c891-4e21-a99d-d10c3e7bdc7e)
</details>
<details>
 <summary> 
  iverilog
 </summary>
I installed iverilog using the following command :  
 
```
 sudo apt-get install iverilog  
```
Below is the screenshot of the successful installation:  
![Screenshot from 2023-07-31 09-45-22](https://github.com/Rachanaka/iiitb-asic/assets/140998470/da2a4199-d7b0-47a6-8e00-989bd6d55640)

</details>
<details>
 <summary>
  gtkwave
 </summary>
 
I installed gtkwave using the following command:  
```
sudo apt update
sudo apt install gtkwave
```
Below is the screenshot of successful installation:
![Screenshot from 2023-07-31 09-51-17](https://github.com/Rachanaka/iiitb-asic/assets/140998470/31e183cb-ab80-49c0-b87c-cc295c44fd56)

</details>  

## Day1 - Introduction to Synthesis using Yosys   

<details>
 <summary> Summary </summary>

This section shows how I simulated and synthesized a 2x1 mux using iverilog and yosys respectively. iverilog generates from the RTL design and its testbench a value changing dump file (vcd). gtkwave is the tool used to plot the simulation results of the design. Yosys is a tool which synthesizes RTL designs into a netlist. It is also used to test the synthesized netlist when we provide it with a testbench.

</details>	
	
<details>
 <summary> Verilog codes </summary>
The verilog codes of the 2x1 mux (good_mux.v) and its testbench (tb_good_mux.v) are taken from https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git

</details>

 <details>
 <summary> Simulation: iverilog and gtkwave </summary>
 
 I used the following commands to simulate and view the plots of the RTL design:
	
 ```bash
 iverilog <name verilog: good_mux.v> <name testbench: tb_good_mux.v>
 ./a.out
 gtkwave tb_good_mux.vcd
 ```
	
 Below is the screenshot of the gtkwave plots:  
 ![Screenshot-3](https://github.com/Rachanaka/iiitb-asic/assets/140998470/0a89c2ff-77c7-4619-b16c-7038006f93e7)
</details>  

<details>
 <summary> Synthesis: Yosys </summary>
	
 In the directory of the verilog files, I used the following commands to synthesize and view the synthesized deisgn:
	
 ```bash
yosys> read_liberty -lib <path to lib file>
yosys> read_verilog <path to verilog file>
yosys> synth -top <top_module_name>
yosys> abc -liberty <path to lib file>
yosys> show
 ```
 Below is the screenshot of the synthesized design:  
 ![Screenshot-1](https://github.com/Rachanaka/iiitb-asic/assets/140998470/ef3abf2d-5fe7-478a-adc8-4d71e10d6ffc)  

 I used the following commands to generate the netlist:
 ```bash
 yosys> write_verilog <file_name_netlist.v>
 yosys> write_verilog -noattr <file_name_netlist.v>
 ```
 
 Below is the screenshot of the generated netlist:  
 ![Screenshot-2](https://github.com/Rachanaka/iiitb-asic/assets/140998470/871e9051-4cc0-40b3-845e-35f6eab87caf)  
 </details>  

 ## Day2 - Hierarchical and Flat Synthesis  
 <details>
 <summary>
Introduction to library files
 </summary>
	<br>
	 This section explains more about the library file that we are particularly using in this program i.e. "sky130_fd_sc_hd__tt_025C_1v80.lib".  	 
Here is the format of the library naming convention -   
sky130_fd_sc_hd__&lt;process_variation>_&lt;temperature_variation>_&lt;voltage_variation>  <br>
<br>
Process variation talks about the variations encountered during the fabrication process. Process variation may include any one of the following values -  <br>
	 - slow  <br>
	 - fast  <br>
	 - typical  <br>
Typical operating conditions used in the library are -  
https://github.com/Rachanaka/iiitb-asic/blob/main/images/operating_conditions.png 
We can also find multiple cells with the definition of same 'and' gate but with variation in the area of the gates. This simple means that the cells with larger area are employing wider transistors. Below is the image of 3 different 'and' gates with their areas, leakage powers and few more details -  <br>
	 
sky130_fd_sc_hd__and2_0  <br>
sky130_fd_sc_hd__and2_2  <br>
sky130_fd_sc_hd__and2_4  <br>

![and_module_comparison](https://github.com/Rachanaka/iiitb-asic/assets/140998470/a6bdacfc-cbdc-4f2a-a6b7-6e76db83b6db)  

 </details>
<details>
	<summary>Hierarchical and Flat Synthesis</summary>
	<br>

 When there are multiple modules in a program that should be synthesized, we can directly synthesize top module or synthesize each sub module separately. Both the processes have their own advantages. We prefer synthesizing each module seperately especially in the following two scenarios -  <br>
 - when we have multiple instantiations of same submodule several times in the main module  <br>
 - When there is a massive design, tool might not work as efficiently as required, we might have to divide and conquer by synthesizing each sub module seperately.  <br>
I have synthesized a verilog code named 'multiple_modules.v' using both hierachical and flat modelling styles.

**Using Hierarchical model**  

In the directory of the verilog files, I used the following commands to synthesize and view the synthesized deisgn:
	
 ```bash
yosys> read_liberty -lib <path to lib file>
yosys> read_verilog <path to verilog file>
yosys> synth -top <top_module_name>
yosys> abc -liberty <path to lib file>
yosys> show <module name>
 ```
Below is the output of the file using hierarchical approach -  <br>
![multiple_module_hier](https://github.com/Rachanaka/iiitb-asic/blob/main/images/multiple_modules_hier.png)

**Using Flat model**  

Now I tried using the command 'flatten' and synthesize the same file  
 ```bash
yosys> flatten
yosys> synth -top <top_module_name>
yosys> abc -liberty <path to lib file>
yosys> show
 ```
Below is the output of the file using Flat approach -  <br>
![multiple_module_flat](https://github.com/Rachanaka/iiitb-asic/blob/main/images/multiple_module_flat.png)
So, it can be clearly seen that, the command 'flatten' in Yosys will traverse through the hierarchy of the design and replace all the instances of submodules with their respective module implementations. This creates a flat representation of the design where there is no longer any hierarchy of the modules.  

Below is the difference between the generated netlists in both the the cases -
![multiple_module_hier_vs_flat](https://github.com/Rachanaka/iiitb-asic/blob/main/images/multiple_module_heir_vs_flat.png)  
		
</details> 
<details>
	<summary>Various Flop coding styles and Optimizations</summary>  
<br>	
	
**Need for Flops**  
In combinational circuits, output of the circuit changes based on the inputs given and during this process there might be unnecessary changes in the output which are called glitches due the propagation delays of the circuit components. To avoid these glitches in combinational circuit,we add flops so that glitches do not pass on to the next combinational block. Basically, flops shield glitches from entering into the next combinational logic block. These flops are to be initialized using reset/set inputs.  
There are 2 types of reset/set inputs -  
1. Asynchronous
   -Asynchronous set - Output of the flop goes high as soon as set is high inspite of the clock signal not being at the positive edge.
   -Asynchronous reset - Output of the flop goes low as soon as reset is high inspite of the clock signal not being at the positive edge.
2. Synchronous
   -Synchronous set - Output of the flop goes high when set goes high and clock signal crosses positive edge.
   -synchronous reset - Output of the flop goes low when reset goes high and clock signal crosses positive edge.
   
   I have synthesized D-flipflop using aynchronous set/reset and Synchronous set/reset inputs using Yosys, ouputs of which are given below -
**Asynchronous Set**
![async_set](https://github.com/Rachanaka/iiitb-asic/blob/main/images/async_set.png)
![async_set_yosys](https://github.com/Rachanaka/iiitb-asic/blob/main/images/async_set_yosys.png)

**Asynchronous Reset**
![asynch_reset](https://github.com/Rachanaka/iiitb-asic/blob/main/images/asynch_reset.png)
![async_reset_yosys](https://github.com/Rachanaka/iiitb-asic/blob/main/images/async_reset_yosys.png)  

**Asynchronous and Synchronous Reset**
In this case, we have given both synchronous and asynchronous resets. Synchronous reset pin acts as an input the flop when clock goes high and asynchronous reset is independent of the clock.
![async_sync_reset](https://github.com/Rachanaka/iiitb-asic/blob/main/images/async_sync_reset.png)
![async_sync_yosys](https://github.com/Rachanaka/iiitb-asic/blob/main/images/async_sync_yosys.png)  

**Synchronous reset**
![Synch_reset](https://github.com/Rachanaka/iiitb-asic/blob/main/images/Synch_reset.png)
![sync_reset_yosys](https://github.com/Rachanaka/iiitb-asic/blob/main/images/sync_reset_yosys.png)  
   
</details>
 
 ## Day3 - ABC  
 

 
 
 
	

