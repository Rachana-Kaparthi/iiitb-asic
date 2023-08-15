This repository summarizes the day to day progress made during the VSD Tapout program -

[Day 0 - Installation of the required tools](#day0---installation-of-the-required-tools)  
[Day 1 - Introduction to Synthesis using Yosys](#day1---introduction-to-synthesis-using-yosys)  
[Day 2 - Hierarchical and Flat Synthesis ](#day2---hierarchical-and-flat-synthesis)   
[Day 3 - Combinational and Sequential Logic Optimisations](#day3---combinational-and-sequential-logic-optimisations)  
[Day 4 - GLS, Synthesis-Simulation mismatch and Blocking/Non-blocking statements](#day4---gls-synthesis-simulation-mismatch-and-blockingnon-blocking-statements)  
[Day 5 - If,Case,For Loop and For generate](#day5---ifcasefor-loop-and-for-generate)
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

Yosys Commands used for synthesis are :
	
 ```bash
yosys> read_liberty -lib <path to lib file>
yosys> read_verilog <path to verilog file>
yosys> dfflibmap -liberty <path to flop lib file>
yosys> synth -top <top_module_name>
yosys> abc -liberty <path to lib file>
yosys> show
 ```
General practice is to maintain a separate library file for standard cells and a separate library file for flop design, so we need to explicitely tell the design from where to pick D flipflop. Hence we use the command -  

```
dfflibmap -liberty <path to flop lib file>
```

</details>

<details>
	<summary>Optimizations</summary>  
Here, I have synthesized multiplier by 2 and multiplier by 9 circuits without any use of the gates. This was possible because multiplying by 2 in binary only meant shifting of the digits to left by single digit and appending 0 at the lsb. Also, by keen observation it can be found that multiplying by 9 means shifting the bits to left 3 times and appending the number at the lsb bits. Consider the below example :  
	
**Multiplication by 2**  
	Let input be a and output be y = a*2,  
If a = 0101, then y = 1010 i.e y = a<<1  
This can be achieved by mere connection of wires from input to output without any use of gates. Infact, linking the library file is also not required in such cases.  
Below are the messages received when trying to link the library -
![Screenshot](https://github.com/Rachanaka/iiitb-asic/assets/140998470/43eaea40-ffda-44a0-b042-676b1d889cd9)

Given below is the output of the synthesizer for the same -  
![mult_2](https://github.com/Rachanaka/iiitb-asic/blob/main/images/mult_2.png)  

**Multiplication by 9**  
Let input be a and output br y = a*9,  
If a = 0101, then y = a*9 = (a*8)+(a*1) = (a<<3)+(a).  
If a is a 3 digit bit then y becomes aa(here 101101) i.e. a is left shifted 3 times and a is added in the place of shifted bit at lsb.  
Given below is the output of the synthesizer for the same -  
![mult_8](https://github.com/Rachanaka/iiitb-asic/blob/main/images/mult_8.png)
</details>
 
## Day3 - Combinational and Sequential Logic Optimisations  
<details>
	<summary>Introduction to combinational and sequential circuits</summary>  
<br>  
In digital logic, we know that there are 2 types of logics, Combinational logic and Sequential logic. Whatever logic we might be using, optimisation of the logic plays an important role in minimizing the area as well as saving the power. So, in this session we are gonna discuss about some optimisation techniques both in combinational logic and sequential logic.  
</details>  

<details>
<summary>Combinational logic optimisation techniques </summary>  
<br>  
1.Constant propagation optimisation <br>  
	     - Direct optimisation  <br>
2.Boolean logic optimisation <br> 
	     - K Map  <br>
  	     - Quine Mckluskey algorithms <br> 
	
**Constant Propagation Optimisation**  
Let us take an example to discuss about this:  
Consider *Y=(AB+C)'*  
If A = 0 then Y = C'  
So from implementing Y logic using and, or gates, if A = 0 logic would be optimised to Y=C' which can be implemented by just an inverter there by reducing area and saving power.  

**Boolean Logic Optimisation**  
Consider a statement where it states as below  
assign *y=a?(b?c:(c?a:0)):(!c)*  
Upon simplication it turns out to be y=ac+a'c' which can be implemented just by nand gate instead of three 2:1 multiplexers by optimising the logic.  

Realizing combinational circuit optimization using some examples in the lab- 

**Example 1:**  
Below is the code present in a file named opt_check:  
```
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```
The above code is synthesized in yosys using following commands-  
```bash
yosys> read_liberty -lib <path to lib file>
yosys> read_verilog <path to verilog file>
yosys> synth -top <top_module_name>
yosys> opt_clean -purge
yosys> abc -liberty <path to lib file>
yosys> show
 ```
Output of the synthesis:  
![opt_check](https://github.com/Rachanaka/iiitb-asic/blob/main/images/opt_check.png)  

Ideally, the code should realize a multiplexer but an and gate is shown instead of a multiplexer because of the command opt_clean that we gave during synthesis. opt_clean -purge cleans up all the unused cells and wires there by giving us an optimised logic.  
**Example 2:**  
Below is the code present in a file named multiple_modules_opt:  
```
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 

endmodule

```
The above code is synthesized in yosys using following commands-  
```bash
yosys> read_liberty -lib <path to lib file>
yosys> read_verilog <path to verilog file>
yosys> synth -top <top_module_name>
yosys> flatten
yosys> opt_clean -purge
yosys> abc -liberty <path to lib file>
yosys> show
 ```
Since the above code contains multiple modules, we should be using flatten command in order to optimise the combinational logic.  

Output of the synthesis:  
![multiple_modules_opt](https://github.com/Rachanaka/iiitb-asic/blob/main/images/multiple_modules_opt.png)  
This code should ideally realize two and gates and an or gate for logic y, but instead it has turned out to be a single and gate and single or gate due to optimising the logic.
</details>  
<details>  
<summary>  
	Sequential logic optimisation techniques  
</summary>
<br>  
1.Basic <br> 
 	   - Sequential constant propagation <br> 
2.Advanced  <br>
	   - State optimisation <br> 
    	   - Retiming <br>
	   - Sequential logic colning (Floor plan aware synthesis)  <br>  
	
**Sequential Costant**  
In sequential circuits, if output of a flop is always constant irrespective of the whatever inputs are given, it is called a sequential constant.  
Consider below example-
![seq_const](https://github.com/Rachanaka/iiitb-asic/blob/main/images/seq_const.jpeg)  
In this example, Q is always 0 irrespective of reset or clock inputs there by making output y= 1 always. but the same example does not hold good if there is set input instead of reset input as Q then can receive either 0 or 1 based on set and clock inputs and is not constant. 

Trying to realize the above logic using some examples in the lab: 

**Example 1**  
Below is the code present in a file named dff_const1.v:  
```
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule
```
**Simulation output:**  
![dff_const1_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const1_simulation.png)  
**Synthesis output**
![dff_const1_synthesis](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const1_synthesis.png)  

**Example 2**  
Below is the code present in a file named dff_const2.v:  
```
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
endmodule
```
**Simulation output:**  
![dff_const2_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const2_simulation.png)  
**Synthesis output**  
![dff_const2_synthesis](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const2_synthesis.png)  

**Example 3**  
Below is the code present in a file named dff_const3.v:  
```
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```
**Simulation output:**  
![dff_const3_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const3_simulation.png)  
**Synthesis output**  
![dff_const3_synthesis](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const3_synthesis.png)  

**Example 4**  
Below is the code present in a file named dff_const4.v:  
```
module dff_const4(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b1;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```
**Simulation output:**  
![dff_const4_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const4_simulation.png)  
**Synthesis output**  
![dff_const4_synthesis](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const4_synthesis.png)  

**Example 5**  
Below is the code present in a file named dff_const5.v:  
```
module dff_const5(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b0;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```
**Simulation output:**  
![dff_const5_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const5_simulation.png)  
**Synthesis output**  
![dff_const5_synthesis](https://github.com/Rachanaka/iiitb-asic/blob/main/images/dff_const5_synthesis.png)  

</details>
<details>  
<summary>  
	Sequential logic optimisation for unused outputs  
</summary>  
<br>  
While synthesizing the code, by default the yosys synthesizer optimises all the intermediate outputs that do not have a role in producing the primary output of the module, thereby reducing the logic. Consider below two examples for better understanding:  

**Example 1**  
```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end
endmodule
```
**Synthesis Output**
![counter_opt](https://github.com/Rachanaka/iiitb-asic/blob/main/images/counter_opt.png)
**Example 2**  
```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0] == 3'b100);

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end
endmodule
```
**Synthesis output**  
![counter_opt2](https://github.com/Rachanaka/iiitb-asic/blob/main/images/counter_opt2.png)  

Both the examples above implements the same 3 bit counter but synthesizer implemented example 1 using only one dff whereas it implemented example 2 using three dffs. This is because the primary output in example 1 depends only on the LSB of the count, hence the other 2 bits of the count are optimised by the synthesizer. But this is not the case in example 2, output in example 2 depends on all the 3 bits of the counter, hence counter is implemented making use of 3 flipflops.  

</details>  

## Day4 - GLS, Synthesis-Simulation mismatch and Blocking/Non-blocking statements
<details>
	<summary> Gate Level Simulation</summary>  
<br>  
	This session answers about what and why is GLS used. To verify our RTL logic we simulate it under testbench and after synthesis we get a netlist which is usually same as RTL logic. But we should also ensure whether the generated netlist is also working as per the requirements. So, GLS is used to verify the logical correctness of the design after synthesis and also it ensures that the timing of the design is met when run with delay annotation.  <br>  
There might be several reasons why netlist does not match with the RTL logic i.e. synthesis and simulation mismatch may occur due to following reasons: <br> 
        1.Missing sensitivity list  <br>
	2.Blocking vs Non blocking assignments  <br>
	3.Non standard verilog coding  <br>  
	
Workflow of GLS is similar to that of verilog simulation but instead of RTL logic as input we provide gate levellogic as input -  
	
![gls_using_iverilog](https://github.com/Rachanaka/iiitb-asic/blob/main/images/GLS%20using%20iverilog.png)  
</details>  

<details>
<summary>Labs on GLS</summary>  
<br>
	
**Example 1**  
Below is the content of the file named ternary_operator_mux.v  
	
```
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
assign y = sel?i1:i0;
endmodule
```  
**Synthesis output:**  
![ternary_operator_mux](https://github.com/Rachanaka/iiitb-asic/blob/main/images/ternary_mux_schematic.png)  
**Netlist file**  
```
module ternary_operator_mux(i0, i1, sel, y);
  wire _0_;
  wire _1_;
  wire _2_;
  wire _3_;
  wire _4_;
  wire _5_;
  input i0;
  input i1;
  input sel;
  output y;
  sky130_fd_sc_hd__clkinv_1 _6_ (
    .A(_0_),
    .Y(_4_)
  );
  sky130_fd_sc_hd__nand2_1 _7_ (
    .A(_1_),
    .B(_2_),
    .Y(_5_)
  );
  sky130_fd_sc_hd__o21ai_0 _8_ (
    .A1(_2_),
    .A2(_4_),
    .B1(_5_),
    .Y(_3_)
  );
  assign _0_ = i0;
  assign _1_ = i1;
  assign _2_ = sel;
  assign y = _3_;
endmodule
```
I have then simulated the generated netlist in iverilog using the below commands:  
```
> iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v <netlist file> <testbench file>
> ./a.out
> gtkwave <generated vcd file>
```
**Simulation Output**  
![ternary_mux](https://github.com/Rachanaka/iiitb-asic/blob/main/images/ternary_mux_gtk.png)  
<br>  
**Example 2: Synthesis-Simulation mismatch due to missing sensitivity list**  
Below is the content of the file named bad_mux.v  
```
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
**Netlist output**  
```
module bad_mux(i0, i1, sel, y);
  wire _0_;
  wire _1_;
  wire _2_;
  wire _3_;
  input i0;
  wire i0;
  input i1;
  wire i1;
  input sel;
  wire sel;
  output y;
  wire y;
  sky130_fd_sc_hd__mux2_1 _4_ (
    .A0(_0_),
    .A1(_1_),
    .S(_2_),
    .X(_3_)
  );
  assign _0_ = i0;
  assign _1_ = i1;
  assign _2_ = sel;
  assign y = _3_;
endmodule
```
**Simulation output:**  
![bad_mux_simulation_output](https://github.com/Rachanaka/iiitb-asic/blob/main/images/bad_mux_simulation_output.png)  
**Synthesis Output**  
![bad_mux_synthesis_output](https://github.com/Rachanaka/iiitb-asic/blob/main/images/bad_mux_synthesis-output.png)  

We can clearly see that both the simulation and synthesis outputs do not match, this is because of missing sensitivity list. The synthesizer by default considers all the inputs in the sensitivity list where as we have only considered select line in the sensitivity list. In fact synthesizer tells us about this during synthesis:  

![bad_mux_msg](https://github.com/Rachanaka/iiitb-asic/blob/main/images/bad_mux_msg.png)  

**Example 3: Synthesis-Simulation mismatch due to blocking/non blocking statements**  
Below is the content of the file named blocking_caveat.v 
```
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```
**Netlist output**  
```
module blocking_caveat(a, b, c, d);
  wire _0_;
  wire _1_;
  wire _2_;
  wire _3_;
  wire _4_;
  input a;
  wire a;
  input b;
  wire b;
  input c;
  wire c;
  output d;
  wire d;
  sky130_fd_sc_hd__o21a_1 _5_ (
    .A1(_2_),
    .A2(_1_),
    .B1(_3_),
    .X(_4_)
  );
  assign _2_ = b;
  assign _1_ = a;
  assign _3_ = c;
  assign d = _4_;
endmodule
```
**Simulation output:**  
![blocking_caveat_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/blocking_caveat_simulation.png)  
**Synthesis Output**  
![blocking_caveat_synthesis](https://github.com/Rachanaka/iiitb-asic/blob/main/images/blocking_caveat_synthesis.png)  

We can clearly see that both the simulation and synthesis outputs do not match, this is because of using blocking statements in sequential circuits.  

</details> 

## Day5 - If,Case,For Loop and For generate  
<details>
<summary>If, case constructs</summary>  
	If statements are nothing but conditional statements, depending upon the condition in the if statement, the code inside the if statement is executed. There can be any number of if else statements. If there is an incomplete if statement then we get inferred latches which is considered as bad coding style.  
	Even in case construct there can be inferred latches due to incomplete case statements and partial assignments in case statements.  
</details>  
<details>
	<summary>Lab: Incomplete If statements</summary>  
	
**Example 1**  
Below is the content of the file imcomp_if.v  
```
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
end
endmodule
```
**Simulation output**  
![incomp_if_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/incomp_if_simulation.png)  
**Synthesis output**  
![incomp_if_schematic](https://github.com/Rachanaka/iiitb-asic/blob/main/images/incomp_if_scematic.png)  
It can be clearly seen that synthesizer has inferred a latch due to absence of else statement.

**Example 2**  
Below is the content of the file imcomp_if2.v  
```
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;

end
endmodule
```
**Simulation output**  
![incomp_if2_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/incomp_if2_simulation.png)  
**Synthesis output**  
![incomp_if2_schematic](https://github.com/Rachanaka/iiitb-asic/blob/main/images/incomp_if2_schematic.png)

It can be clearly seen that we are expecting a 2:1 mux structure and we got a latch as output due to incomplete if statement.  

</details>  
<details>
	<summary>Lab: Incomplete Overlapping Case statements</summary>  
	
**Example 1**  
Below is the content of the file imcomp_case.v  
```
module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule
```
**Simulation output**  
![incomp_case_simulation](https://github.com/Rachanaka/iiitb-asic/blob/main/images/incomp_case_simulation.png)  
**Synthesis output**  
![incomp_case_schematic](https://github.com/Rachanaka/iiitb-asic/blob/main/images/incomp_case_schematic.png)  
So, in this example it can be seen that for select=10 or 11, output infers a latch due to incomplete case statement.  

</details>
 
	

