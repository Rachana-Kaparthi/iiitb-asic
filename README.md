This repository summarizes the day to day progress made during the VSD Tapout program -

[Day 0 - Installation of the required tools](https://github.com/Rachanaka/iiitb-asic/blob/main/README.md#day0---installation-of-the-required-tools)  
[Day1 - Introduction to Synthesis using Yosys](https://github.com/Rachanaka/iiitb-asic/blob/main/README.md#day1---introduction-to-synthesis-using-yosys)  
[Day 2](#day-2)  
[Day 3](#day-3)
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

 
 
 
	

