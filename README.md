# miit_fdp

 ## Day 1 - Introduction to Verilog RTL design and Synthesis 

  ### Introduction to iverilog design test bench

Simulator: tool used for simulating the design.
RTL Design is checked for adherence to the spec by simulating the design.
Design : the actual verilog code or set of verilog codes which has the intended functionality to meet with the required specifications.
![image](https://user-images.githubusercontent.com/123365818/214258651-1ecf6b07-b6f8-424e-95fd-ef2c657e2ea2.png)

Test_bench: the setput to apply the stimulus (test vector) to the design to check its functionality
![image](https://user-images.githubusercontent.com/123365818/214258781-80db0540-cd8c-499a-b1b2-016e08af44da.png)

   ### Labs using iverilog and gtkwave
1. Installation the opensource software Virtual box for ruuning the Linux Ubuntu without actually installing it.
	 mkdir vsd  
 	cd vsd  
 	git clone https://github.com/kunalg123/vsdflow.git  
 	mkdir vlsi  
 	cd vlsi  
 	git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git  
 	cd SKY130RTLDesignAndSynthesisWorkshop  
 	cd my_lib  
 	cd lib  
 	cd ..  
 	cd verilog_model  
 	cd ..  

 ![Capture1](https://user-images.githubusercontent.com/123365818/214260092-934b6dfb-cecc-4244-a741-793293eb37d2.PNG)

 	cd ..  
	cd verilog_files 
 
2. simulation the RTL design and assosciated test bench.
	iverilog good_mux.v tb_good_mux.v
	./a.out
	gtkwave tb_good_mux.vcd
 ![Capture3](https://user-images.githubusercontent.com/123365818/214260262-f5a6c040-e5da-4dd1-aa14-877fe1e74078.PNG)
 

	Trace the transitions for selected variable
 
![Capture5](https://user-images.githubusercontent.com/123365818/214260812-0dd6e85b-1356-4b0e-be35-db88f1193698.PNG)

	View our Verilog RTL design and testbench code using

	vim tb_good_mux.v -o good_mux.v

![Capturevim1](https://user-images.githubusercontent.com/123365818/214261023-1eaa6f6d-e572-4fa6-8e19-29f078106265.PNG)

  ### Introduction to Yosys and Logic synthesis
  
**Synthesizer** :It is a tool used for the conversion of an RTL to a netlist.

**Netlist** : It is a representation of the input design to yosys in terms of standard cells present in the library. Yosys is the Synthesizer tool that we will be using. Diiferent levels of abstraction and synthesis
![image](https://user-images.githubusercontent.com/123365818/214261468-9e7ad23e-3e6d-4e9a-9f81-c5c4c9fdc200.png)
![image](https://user-images.githubusercontent.com/123365818/214261856-9ce803da-34c5-4429-9b22-0a3ffe34da54.png)

During Logic Synthesis an RTL code is transformed into a gate level netlist.

A synthesizer performs gate level translation.
Design is converted into gates and connections are made between the gates.
The file what we write out finally is a gate-level netlist.

.lib is the collection of logic modules which includes basic logic gates such as AND, OR, NOT, etc. It also contains different flavours of the same gate.
  
#### Setup time Analysis

It should take 1 CLK cycle for the signal to propagate from the Launch DFF-A to the Capture DFF-B through the combimational circuit.
While this propagation all delays : Propagation delay of DFF A (Tcq-a)+ Propagation delay of combinational circuit(Tcomb)+Setup time of DFF-B(Tsetup_b). Thus ,the constraint becomes:

		Tclk>Tcq-a+Tcomb+Tsetup_b
		
**Setup time** is the time for which data should arrive before the launch clock edge to be reflected at the output reliably.

For high performance we need high speed gates. So,the frequency of a gates should be high or the Tclk should be less. So all the above mentioned delay should be less. 

 ![image](https://user-images.githubusercontent.com/123365818/214262680-103e1268-c5e2-4fce-8b6a-8188aacfc182.png)
 
#### Hold up time Analysis

After the after the edge of the clock I don't want data to change . So, the fastest change that can happen at the input of we would be after the hold time window so that I can reliably capture the previous clock edge of launch flop DFF-A to ensure there are no hold issues at DFF-B . 
 		**Thold_b<Tcq-a+Tcomb**
		
 ![image](https://user-images.githubusercontent.com/123365818/214262923-574ba77b-3371-4f87-be73-2138b30196c2.png)

#### Comparison of Faster Cells and Slower Cells

The load for a cell in a digital logic circuit usually is taken as a capacitor.
Propagation delay is the time required for the input to be reflected in the output of the cell it depends on the gate driving capacity which is dependent on the capacitance. Faster the charging and discharging of a capacitance, lesser will be the cell delay. In order to charge/discharge the capacitance fast, transistors are needed which are capable of sourcing more current which means it is a wide transistor as the current carrying capacity of a transistor is the function of its width. Wider the transistors, lower will be the delay but more is the area and power. Narrow Transistors will have more delay but less area and less power.

So fastercells don't come free. They come at the tradeoffs of area and power.
 
### Labs using Yosys and Sky130 PDKs

Commands to obtain a synthesized implementation of good_mux design

1.To read verilog files, .lib files and writing out the netlist after invoking yosys. 
	
	yosys

![yosis1](https://user-images.githubusercontent.com/123365818/214263866-e43c378f-25fa-45b3-babc-752936f11143.PNG)

2.Read the library using the read -liberty command. 

	read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

	![Yosys2](https://user-images.githubusercontent.com/123365818/214264496-aa53d692-d245-46f7-8e0a-8aa4906e3a3e.PNG)

3. Reading the verilog design file. 

4. Generating the netlist 
	
	read_verilog good_mux.v

5. Synthesize the rtl code with the top module's name using synth -top command.

	synth -top good_mux
	![Yosys3](https://user-images.githubusercontent.com/123365818/214264544-b0bf9d5f-5c87-4b41-997c-2603cb910b14.PNG)

6. Use abc -liberty to generate the netlist. 

	abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

	![Yosys4](https://user-images.githubusercontent.com/123365818/214264631-a0bdb462-b52f-4624-8a92-5edf712d9db5.PNG)


7. Display the synthesis implementation using show.
	
	show

	![yosysshow](https://user-images.githubusercontent.com/123365818/214264660-61cf37a0-0c17-4002-ba44-9aefdd196ffb.PNG)

#### Commands to write a netlist

	write_verilog -noattr good_mux_netlist.v
	
	!vim  good_mux_netlist.v

	![yosisnetlist](https://user-images.githubusercontent.com/123365818/214264824-22b5ea78-c964-42f2-b6d9-c08ba47ef3b6.PNG)

## Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

  ### SKY130RTL D2SK1 - Introduction to timing .libs

To study the name of the library

	Command: vim ../my_lib/lib/SKY130_fd_sc_hd__tt_025C_1v80.lib
	
![Capture1](https://user-images.githubusercontent.com/123365818/214477368-67cff53e-f4f0-47c2-9ce5-efe06a096bf4.PNG)

**TT** : it stands for typical.

The important three parameters of the library:

**P**- Process:The variations due to fabrication
**V** -Voltage: Variations in voltage also impact the behavior of the circuit
**T**- Temperature :Semiconductors are very sensitive to temperature variations too. All this variations determine the performance of a silicon whether it is fast or slow. 

Turning off the red color

 Command : syn off
 
The technology and units of all the parameters can be seen in the lib file.

![Capture2](https://user-images.githubusercontent.com/123365818/214477750-f94770d6-9cb2-4057-9363-f8f48d4702bc.PNG)

The voltage, process and temperature values are also specified.

![Capturevpt](https://user-images.githubusercontent.com/123365818/214478521-cd1da395-6699-4247-adea-4b1c2454df23.PNG)

Different flavors of this same as well as different types of cells.

![Capturedifferent](https://user-images.githubusercontent.com/123365818/214479825-deb7c282-e0d1-491f-b92d-70d548aa745b.PNG)

The name a21110 signifies that it's And OR gate where in the first two inputs A1 and A2 are And'ed. It's result is OR'ed with the rest of the three inputs B1,C1 and D1. 

The library describes the different features of the cell like its leakage power,the various input's combinations and the operations between them.

![Capturediff1](https://user-images.githubusercontent.com/123365818/214480038-2dbd1802-cfc1-42d0-a5c4-2d1b734e1027.PNG)

To understand the functionality of the cell, it needed to look into the equivalent verilog model. Each of the input can take a high or a low power level.
For 5 inputs, there are 32 combinations in total. The behaviour model specifies the delay and power for each of these inputs. 
Inside the cell block,it can specified different power combinations and their respective leakage power values. It also shows the area. 
It gives the description of various pin in terms of their capacitance transition , internal power and the delay associated with this pins.

![CaptureCap](https://user-images.githubusercontent.com/123365818/214480933-f9c62e59-9686-405a-b281-a329347ff7fd.PNG)

For better understanding, a small gate (two input AND) is choosen to see it's behaviour view.

![Captureand](https://user-images.githubusercontent.com/123365818/214482018-f242f393-d638-464d-99e1-09ed286592ed.PNG)

Four possible combinations the leakage power and the logic levels of which are specified.

It can be compared between the two input and gates (1,2,4).

![Captureand1](https://user-images.githubusercontent.com/123365818/214483776-d5022338-f901-4165-b817-9d36b83c7b6a.PNG) ![Captureand2](https://user-images.githubusercontent.com/123365818/214483964-622f7224-532a-4e65-976d-063628b7cfb5.PNG) ![Captureand4](https://user-images.githubusercontent.com/123365818/214484733-59462614-1885-4b62-ba5d-11121ecb8fc5.PNG)

According to the comparison, the area of the and gate "and2_4" is larger than the area of the and gate "and2_2" which in turn has more area with the and gate "and2_0". because of and2_4 employs wider transistors. These are the different flavours of the same and gate. And and2_4 being the widest also has large leakage power values as well as large area. But it will have small delay values as it is faster.

### SKY130RTL D2SK2 - Hierarchical vs Flat Synthesis

To understand the multiple modules are present while synthesizing, the sysnthesis can be done in two forms.
	![hier mult](https://user-images.githubusercontent.com/123365818/214486111-d5280aa5-cb0e-4d30-8fbb-db8ca2f03798.PNG)

Sub_module 1 and sub_module 2 can be seen in above vim window.

It has two some moduels. The module 1 is an OR gate ,sub module 2 is AND gate. The sub module called multiple modules instantiates sub module 1 as u1 and sub module 2 as u2. It has three inputs a b c and an output y.

![Capturesubmodule1](https://user-images.githubusercontent.com/123365818/214487248-f6a0ec7b-49db-46e5-9825-4b3d77015606.PNG)

![Capturemultiplesynth](https://user-images.githubusercontent.com/123365818/214487314-75fe8ac0-f762-4262-ad22-56d88a9a6dd0.PNG)

It also specifies the type and number of cells in a synthesis of RTL design.

![Capturesynthesis](https://user-images.githubusercontent.com/123365818/214487668-e67df9dc-8ddf-49c2-8da5-e1c1e565207b.PNG)

The report has inferred submodule1 having one AND gate ,submodule2 to having one OR gate and multiple module having two cells . 
Now this design is linked to the library using abc command. 
To display the graphical version,

	show multiple_modules

On execution of show command,a synthesized implementation of multiple_modules design is visible.

![Captureshow](https://user-images.githubusercontent.com/123365818/214488480-a3e3e6f2-18d4-4f1a-aa5b-f27f81e918c3.PNG)

Instead of or and and gates it shows the instances u1 and u2 while preserving the hierarchy. This is called the hierarchical design.

![yosyswritenetlist](https://user-images.githubusercontent.com/123365818/214488903-fc185813-6999-419a-9630-bb1e8b93ed51.PNG)

![Yousys](https://user-images.githubusercontent.com/123365818/214490023-67436daf-6bc2-4ede-93a9-6062f6358735.PNG)

![yosysnetlistgenerate](https://user-images.githubusercontent.com/123365818/214489769-4f6a896e-0490-4db4-9063-59654ed8b72d.PNG)

Instead of or, and the circuit is implemented using nand and inverter gates. 
It is always prefered stacked NMOS's(nand gates)to stacked the PMOS's(nor cascaded with inverter for or). Because pmos has a very poor mobility and therefore they have to be made quite wide to obtain a good logical effort .
It can be used flatten to generate a flat netlist. 

**flatten**
 **write_verilog -noattr multiple_modules_flatten_netlist.v**

![flatten](https://user-images.githubusercontent.com/123365818/214491064-8620c421-e6cf-4a94-bf50-89b2cf7c32cc.PNG)

![flatten1](https://user-images.githubusercontent.com/123365818/214491394-f59ff1fb-4cb1-4397-ac6c-7334563918b6.PNG)

Even in the design view using show command, it can be seen that it simply displays the structure completely without any hierarchy.
show multiple_modules

![flatten3](https://user-images.githubusercontent.com/123365818/214492017-97018b7a-f94c-4eaa-98c7-9064daa276db.PNG)

Here there are no instances of U1 and U2 and hierarchy is not present.

### SKY130RTL D2SK3 - Various Flop Coding Styles and optimization

   #### Why Flops and Flop coding styles

More the combinational circuits can occur more glitch in the output. 
That is why an element is needed to store the value of the output and that element is called FLOP(storage element). Flop provides resistance to glitches as they transition only at the clock edges. Even though the input of the flop is glitching ,the output will be stable. This avoids glitch propagation in further combinational circuits. 

![image](https://user-images.githubusercontent.com/123365818/214586866-5b036ac8-707a-40db-9bf1-577cf7a75f4e.png)

![image](https://user-images.githubusercontent.com/123365818/214587086-0007299d-7b01-4b2b-90e7-697873467af6.png)


###### what is glitch?

Glitches are the unwanted or unexpected transactions that occur due to propagation delays in digital circuits. Glitches occur when an input signal changes state, provided the signal takes two or more paths for circuit and both paths have unequal delays. The higher delay on one of the parts can cause a glitch when the single pass arrive at the output gate.

Also,Initialising the flop is required else the combination circuit will evaluate it to a garbage value. To initialise the flop, reset and set pins can be found. These two pins can be either synchronous or asynchronous.

### Asynchoronous and Synchronous resets

Asynchronous set: this set signal does not wait for a clock. The moment asynchronous reset is signal received output queue becomes 1 irrespective of the clock.

Asynchronous reset: this reset signal does not wait for a clock .The moment asynchronous reset signal is received output queue becomes 0 irrespective of the clock.

Verilog codes of asynchronous reset :

![diff](https://user-images.githubusercontent.com/123365818/214514554-1d0a9b5b-9c68-44bb-b384-8dbf7afeb29f.PNG)

GTKWAVE RTL Simulation and Observations :

![dff](https://user-images.githubusercontent.com/123365818/214512607-34a7ac9e-f970-45ff-ba86-0ef3c62c31cc.PNG)

Verilog codes of asynchronous set :

![Asyn_set_vim](https://user-images.githubusercontent.com/123365818/214529620-793670a7-710e-4a43-b7c0-34c4edce67c7.PNG)

GTKWAVE RTL Simulation and Observations :

Synchronous Set:

when async_set=0,Q follows d only at the posedge of the clock.

![Asyn_set0](https://user-images.githubusercontent.com/123365818/214528718-2fe82d80-0b1d-440d-ac5f-05d7ad450129.PNG)

when async_set=1,Q becomes 1 without waiting for the next edge of the clock.

![Asyn_set](https://user-images.githubusercontent.com/123365818/214527724-2774e65c-395a-4704-97e7-bc6766f29f69.PNG)

Synthesis implementation results :

asynchoronous reset :

![dffsync](https://user-images.githubusercontent.com/123365818/214518276-a29c10b3-0895-4c64-9c34-3e92591f44a7.PNG)

asynchronous set:

![Asyn_set1](https://user-images.githubusercontent.com/123365818/214531275-56f430dd-3171-4d7f-addf-7140aef0f10f.PNG)

dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

show dff_async_set

![libmap_set](https://user-images.githubusercontent.com/123365818/214535396-f3a92d0b-e7c7-47d9-9ed2-85da3855ec1a.PNG)

![Asyn_reset_libmap](https://user-images.githubusercontent.com/123365818/214536557-35f22761-4af5-4bd9-b83e-8a23f9fa86fc.PNG)

#### OPTIMISATIONS

vim mult_*.v -o

![multby2](https://user-images.githubusercontent.com/123365818/214590091-54c65636-9d00-45c2-908f-d2b3e330f3d1.PNG)

read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog mult_2.v

synth -top mul2

![mul2](https://user-images.githubusercontent.com/123365818/214592011-81f2bf79-3a44-430b-9dd1-51fd0af2224c.PNG)

show

On synthesizing the netlist and look at its graphical realisation , the same optimisation is occuring in the netlist.
![mul2](https://user-images.githubusercontent.com/123365818/214592592-5ac9ec48-9bb3-409e-9c59-b8f6d62b248d.PNG)
There is no hardware required fot it.
![image](https://user-images.githubusercontent.com/123365818/214593530-8268d1dc-e0f2-4842-8202-996dc2761d0b.png)

abc -liberty ../lib/sky130_fd_sc_hd_tt_025C_1v80.lib  
write_verilog -noattr opt_check_netlist.v 
show
![mul8](https://user-images.githubusercontent.com/123365818/214616067-4124dc5d-861c-48a1-9fc8-a4871a1e39d2.PNG)

## Day 3 - Combinational and sequential optmizations

### SKY130RTL D3SK1 - Introduction to optimizations

The simulator performs many types of optimisations on the combinational and sequential circuits to make a optimized digital circuit design interms of area and power.

***Combinational optimisation methods:***

* Squezzing the logic to get the most optimised design
 * Area and Power savings
* Constant propogation
 * Direct Optimisation
* Boolean Logic Optimisation
 * K-map
 * Quine-mckluskey Algorithm
 
![image](https://user-images.githubusercontent.com/123365818/214612874-37159ee4-acc3-4f19-be61-898eebe0660b.png)
![image](https://user-images.githubusercontent.com/123365818/214613107-15ae3dc6-077c-4866-bd34-601fa7ce58f0.png)

***Sequential optimisation methods:***

* Basic
 * Sequential constant Propogation
* Advanced
 * State Optimisation
 * Retiming
 * Sequential Logic Cloning (Floor Plan Aware Synthesis)
 
![image](https://user-images.githubusercontent.com/123365818/214613867-c166801c-9676-4933-afca-0d2e3a441853.png)
![image](https://user-images.githubusercontent.com/123365818/214614411-d96a7b39-e825-4701-8b75-be2a378c235b.png)

### Combinational Logic Optimisations
All the optimisation examples files are under the verilog_files directory.
ls *opt*
vim opt_check*.v -o
![opt-vim](https://user-images.githubusercontent.com/123365818/214618398-db31c423-2987-4a1e-9934-87b516aef547.PNG)
 1. Case 1
  **assign y=a?b:0;**
Ideally ,the above ternary operator should give us a mux. 

![image](https://user-images.githubusercontent.com/123365818/214619776-619d4857-a557-4834-ae99-3f0692be1da9.png)

But the constant 0 propagates further in the logic. Using boolean simplification , it can be obtained as y = ab.

##### Synthesizing this in yosys :

Before realising the netlist, we must issue a command to yosys to perform optimisations. It removes all unused cells and wires to prduce optimised digital circuit.This can be done using the **opt_clean -purge** command as shown below.

![opt_clean](https://user-images.githubusercontent.com/123365818/214621433-65ca2118-0060-4ec5-8df8-d84c30d942df.PNG)
Observation : After executing **synth -top opt_check**, it can be seen in the report that one AND gate has been inferred.
Then,
 * abc -liberty ../lib/sky130_fd_sc_hd_tt_025C_1v80.lib  
 * write_verilog -noattr opt_check_netlist.v 
 * show
On viewing the graphical synthesis realisation , it can be seen the Yosys has synthesized an AND gate as expected.
![AND](https://user-images.githubusercontent.com/123365818/214623016-bd9f6a8d-bd0a-4682-a130-6d3446ef898e.PNG)

2. Case 2
 **assign y=a?b:1;**
Ideally ,the above ternary operator should give us a mux. 
![image](https://user-images.githubusercontent.com/123365818/214623854-68f964b7-a409-4c5b-82e1-3f52b5ef4adf.png)

But the constant 1 propagates further in the logic. Using boolean simplification , it can be obtained as y = a+b.
On viewing the graphical synthesis realisation , it can be seen the Yosys has synthesized an OR gate as expected.

![OR](https://user-images.githubusercontent.com/123365818/214625403-f4849e04-8077-4d61-9c7e-6ac5ec994af7.PNG)

**assign y = a?(c?b:0):0;**
The output to be a 3 input AND gate based on constant propagation and boolean logic optimisation.The output y can be simplified to y = abc.

![image](https://user-images.githubusercontent.com/123365818/214628384-5ab8c05f-33ce-40c5-95fe-f3c97c2741ad.png)

Next we generate the netlist and observe its graphical representation after synthesis
![ASD3](https://user-images.githubusercontent.com/123365818/214628691-ba214bd4-cc9d-4379-8c15-832943513907.PNG)

**assign y = a?(b?(a & c ):c):(!c);**
The output to be a 2 input XNOR gate based on constant propagation and boolean logic optimisation.The output y can be simplified to y = a xnor c.

![XNOR](https://user-images.githubusercontent.com/123365818/214630835-0c1f458b-94dd-403a-813f-83d87f29affe.PNG)

In multiple module case,


module sub_module1(input a , input b , output y);


	assign y = a & b;
	

endmodule



module sub_module2(input a , input b , output y);

	
	assign y = a^b;
	

endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);


wire n1, n2, n3;



sub_module1 U1 (.a(a), .b(1'b1), .y(n1));

sub_module2 U2 (.a(n1), .b(1'b0), .y(n2));

sub_module2 U3 (.a(b),  .b(d), .y(n3));


assign y =c | (b & n1);


endmodule


![multiplemodule](https://user-images.githubusercontent.com/123365818/214633220-be46fb2f-d129-40f0-af4e-26078cf96496.PNG)

there are two sub modules in the vim.

After sysnthesizing,
![multiple](https://user-images.githubusercontent.com/123365818/214633426-ec744c83-51af-434d-a65c-e79eff1ef86a.PNG)

Another Case

module sub_module(input a , input b , output y);

 assign y = a & b;
 
endmodule

module multiple_module_opt2(input a , input b , input c , input d , output y);

wire n1,n2,n3;

sub_module U1 (.a(a) , .b(1'b0) , .y(n1));

sub_module U2 (.a(b), .b(c) , .y(n2));

sub_module U3 (.a(n2), .b(d) , .y(n3));

sub_module U4 (.a(n3), .b(n1) , .y(y));


endmodule

~ 

![multiple2](https://user-images.githubusercontent.com/123365818/214658593-057cb4a2-d928-4c97-8382-792614f31009.PNG)


![multiple2synth](https://user-images.githubusercontent.com/123365818/214658656-83b73bd1-df26-4fdb-b1eb-201a31427abc.PNG)

### Sequential Logic Optimisations

To understand each of the sequential optimisations through different RTL code examples, the synthesis implementation is checked through yosys to understand how the optimisations take place.

All the optimisation examples are in files dff_const2.v,dff_const3.v,dffconst4.v and dff_const5.v which are under the verilog_files directory.

![dffvim](https://user-images.githubusercontent.com/123365818/214756357-4f3f6a2e-db28-4106-8e80-fc5f5cfc6b4a.PNG)
Above the figure,

**Case 1: dff_const1.v**

module dff_const1(input clk, input reset, output reg q);

always @(posedge clk, posedge reset)

begin

	if(reset)
	
		q <= 1'b0;
		
	else
	
		q <= 1'b1;
		
end

endmodule

Here, it appears that the output Q should be equal to an inverted reset or Q=!reset. 
However, as the reset is synchronous,even if the flop has D pinned to logic 1,when reset becomes 0, Q does not immediately goto 1. It waits untill the positive edge of the next clock cycle.
This is observed by simulating the design in verilog, and viewing the VCD with GTKWave as follows

![const1](https://user-images.githubusercontent.com/123365818/214756440-416cf7bc-52b3-422e-8b8e-b3949861b7b9.PNG)

In the gtk waveform above , when reset becomes 0, Q becomes 1 at the next clock edge. Since Q can be either 1 or 0, a sequential constant cannot be received, and no optimisations should be possible here. 
To verify it using Yosys synthesis and optimisation.
It is needed to use in synthesising process

**dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib**

dfflibmap is a switch that tells the synthesizer about the library to pick sequential circuits( mainly Dff's and latches) from.

And then generate the netlist

* abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
* write_verilog -noattr dff_const1_netlist.v 
* show

![constsys](https://user-images.githubusercontent.com/123365818/214758740-3fa0d88e-3b99-4a36-a8ee-c70cd27f31a2.PNG)

According to above figure, no optimisation is performed in th yosys implementation during synthesis.

**Case 2 : dff_const2.v**

module dff_const2(input clk, input reset, output reg q);

always @(posedge clk, posedge reset)

begin

	if(reset)
	
		q <= 1'b1;
		
	else
	
		q <= 1'b1;
		
end

endmodule

Regardless of the inputs, the output q always remains constant at 1 .

This is observed by simulating the design in verilog, and viewing the VCD with GTKWave as follows

![Const2](https://user-images.githubusercontent.com/123365818/214756975-8d2cfc8e-50d2-4a7b-9df6-b582c1bba5be.PNG)

Since the output is always constant ie Q=1, it can easily be optimised during synthesis.

![const2sys](https://user-images.githubusercontent.com/123365818/214758880-80c8edc2-0b1b-4d09-951a-ae3b25379cc9.PNG)

**Case 3**
The RTL code is 

![vimconst3](https://user-images.githubusercontent.com/123365818/214761994-8154a459-a658-459a-9ff7-d79a3165d234.PNG)

The GTK wave

Synthesis with yosys

![Const3sys](https://user-images.githubusercontent.com/123365818/214762224-5234514a-75a7-4d62-9a58-dd5060a52cc1.PNG)

Both the D flip-flops are present in the synthesized netlist.

**Case 4**

The RTL code is 

![Const4vim](https://user-images.githubusercontent.com/123365818/214762075-e342d4e8-3d03-4b28-b19c-d8aeea227c34.PNG)

Independent of the input and reset, Q1 is always going to be constant i.e. Q1=1 . As q can only be 1 or q1 depending on the reset input , but q1 = 1 .Thus q is also constant at the value 1. it can be confirmed this with the simulated waveforms as shown below.

![Const4](https://user-images.githubusercontent.com/123365818/214761417-0533db74-51af-4066-95fa-fac5e25a350b.PNG)

It can easily be optimised using Yosys as shown in the graphical representation because of constant output.

![Const4sys](https://user-images.githubusercontent.com/123365818/214761792-2ba64ba3-a77b-4f61-b8a1-b4deecf98b4c.PNG)

## Day 4 - GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

### Gate Level Simulations

**What is GLS?**

Running the test bench with Netlist as Design Under Test.
Netlist is logically same as RTL code. 
* Same test bench will align with the design.

**Why GLS?**
* Verify the logical correctness of the design after synthesis
* Ensuring the timing of the design is met
	* For the GLS need to be run with delay annotation

Validate the RTL design by providing stimulus to the testbench 
Check whether it meets the specifications earlier we were running the test bench with the RTL code as the design under test
But now under Gate Level Simulation,the netlist is applied to the testbench as desh under test. What it is done at the behavioral level in the RTL code got transformed to the net list in terms of the standard cells present in the library. So, net list is logically same as the RTL code. They both have the same inputs and outputs so the netlist should seamlessly fit in the place of the RTL code. The netlist is putting in place of the RTL file and run the simulation with the test bench.
When simulation is done with the help of RTL code there is no concept of timing analysis such as the hold and setup time which are critical for a circuit. For meeting this setup and hold time criteria there are different flavours of cell in the library.

**GLS Using iverilog**

![image](https://user-images.githubusercontent.com/123365818/214803242-3c140fff-4639-44a6-8981-fa7fcca541a4.png)

In GLS using iverilog flow, the design is a netlist which is given to Iverilog simulator in terms of standard cells present in the library. In the library, there are different flavours of the same type of cell available. To make the simulator understand the specification of the different annotations of the cell, the GATE level verilog models is also given as an input. If the GATE level models are timing aware (delay annotated),then the GLS can be used for timing validation as well.

Question : If netlist is the true representation of the RTL code then what is the need of functional validation of the net list?
Answer: Because they can be simulation and synthesis mismatches.

![image](https://user-images.githubusercontent.com/123365818/214917046-0a0c4899-2335-4935-9a97-9cd284b56d23.png)


### GLS, Synthesis Simulation Mismatches

* Missing sensitivity list

* Blocking and non blocking statements

* Non Standard Verilog Coding

**Missing sensitivity list**
![image](https://user-images.githubusercontent.com/123365818/214919029-af61cf3f-4307-459a-a9f9-789742ee80c3.png)

Simulator functions on the basis of activity that is it looks for if either of the inputs change. If there is no change in the inputs the simulator won't evaluate the output at all.

Example:

module mux(input i0, input i1, input sel, output reg y);

always @(sel)

begin
	
	if (sel)
	
	begin
		
		y = i1;
	
	end
	
	else
	
	begin
		
		y = i0;
	
	end

end

endmodule

The problem in this two input multiplexer code is that simulation happens only when the select is high so if select is slow and there are changes in i0 and i1 they get completely missed. So for the simulator this marks is as good as a latch a double edge flop but the synthesizer does not look at the sensitivity list rather on functionality creates a mux. Simulation: latch Synthesis:mux That is why mismatch is happen.

**Blocking and non blocking statements Inside always block**
Inside always blocks
* = -> Blocking
	* Executes the statements in the order it is written 
	* So the first statement is evaluated before the second statement
	
* <= ->Non Blocking 
	* Executes all the RHS when always block is entered and assigns to LHS. 
	* Parallel evaluation

![image](https://user-images.githubusercontent.com/123365818/214921718-4c1486a6-cdd1-48d2-abc7-3747fafc6f0f.png)

Example of Blocking assignments:

module shift_register(input clk, input reset, input d, output reg q1);
reg q0;

always @(posedge clk,posedge reset)
begin
	if(reset)
	begin
		q0 = 1'b0;
		q1 = 1'b0;
	end
	else
	begin
		q0 = d;
		q1 = q0;
	end
end
endmodule
In this case, D is assigned to Qo not which is then assigned to Q. Due to optimisation a single latch is formed where Q is equal to D.

Example of Non-Blocking assignments:

In the above RTL code if

      begin
		q0 <= d;
		q1 <= q0;
      end
In the non blocking assignments all the RHS are evaluated and parallel assigned to lhs irrespective of the order in which they appear. So it will always be goot a two flop shift register.
![image](https://user-images.githubusercontent.com/123365818/214923197-acacf65f-9322-4069-81b7-57fbbd42534c.png)

Therefore, non blocking statements should be used for writing sequential circuits.

Other example:

![image](https://user-images.githubusercontent.com/123365818/214924063-02579655-cbae-4708-b39c-2341470e31ec.png)

module comblogic(input a, input b, input c, output reg y);

reg q0;

always @(*)

begin

	y = q0 & c;
	
	q0 = a|b;
	
end

endmodule

Look into the always loop whenever any of the inputs a and b or C changes but Y is assigned with old Qo value since it is using the value of the previous Tclk ,the simulator mimics a delay or a flop. Where as, during synthesis, it can be seen the the OR and AND gates as expected.
Therefore, while using blocking statements in this case,we should evaluate Q0 first and then Y so that Y takes on the updated values of Qo. Although both the circuits on synthesis give the same digital circuit comprising of AND, OR gates. But on simulation we get different behaviours.

### SKY130RTL D4SK2 - Labs on GLS and Synthesis-Simulation Mismatch

#### SKY130RTL D4SK2 L1 Lab GLS Synth Sim Mismatch

**Ternary operator**
![image](https://user-images.githubusercontent.com/123365818/214925636-7120a11d-c0b6-4055-abad-1abe31907d17.png)

<cond> ? <True>:<False>

**Case 1: A mux designed with the help of ternary operator**


module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	
	assign y = sel?i1:i0;
	
endmodule
	
To write the RTL code
	
* vim ternary_operator_mux.v

![RTL code](https://user-images.githubusercontent.com/123365818/214931040-295750ec-6ded-4c4b-bea1-bdbe6695ae73.PNG)

* iverilog ternary_operator_mux.v -o tb_ternary_operator_mux.v
* ./a.out
	
To see the waveform of RTL simulation, execute the following command further
	
* gtkwave tb_ternary_operator_mux.vcd

![Ternary_wave](https://user-images.githubusercontent.com/123365818/214930518-afe10754-e72e-4875-92a0-6bc2f0cdfa6d.PNG)

The synthesized netlist for the above,using yosys
	
![ternary_mux](https://user-images.githubusercontent.com/123365818/214932308-94f2e634-4ac0-48e3-8d3d-1fde905f7450.PNG)

To invoke GLS,

Need to read the netlist file and the test bench file assosciated with it.
Need to read 2 extra files that contain the description of verilog models in the netlist.
	
**iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v**

To see the waveform of RTL simulation,
	
./a.out
	
gtkwave tb_ternary_operator_mux.vcd
	
![GLS_ternary_wave](https://user-images.githubusercontent.com/123365818/214934539-87823eb4-2992-428c-8f82-c9016d186bfc.PNG)

The generated netlist does behave like a 2X1 multiplexer.

**Case 2: Missing sensitivity list**

module bad_mux (input i0 , input i1 , input sel , output reg y);
	
always @(sel)
	
begin
	
	if(sel)
	
		y <= i1;
		       
	else
		       
		y <= i0;
	
end 
	
endmodule
	
![bad_vim](https://user-images.githubusercontent.com/123365818/214940506-74a4bfd4-9758-425c-b12b-66952d3c9fa8.PNG)
	
In this example,javascriptalways is evaluated only if javascriptselect is high.It is insensitive to javascript io or i1 because javascriptselect is low.

This is verified by GTKWAVE of RTL Simulation below :

![bad_wave](https://user-images.githubusercontent.com/123365818/214938930-dbbcc9b3-2218-4f3d-ab64-38a44d329824.PNG)

The design simulates a latch rather than a 2x1 mux.
![bad_mux](https://user-images.githubusercontent.com/123365818/214939722-456663b6-d3c2-4a1c-a9f9-c85c72876e99.PNG)
	
Implementing it's GATE level netlist through GLS and observing the waveform,
![bad_gls_wave](https://user-images.githubusercontent.com/123365818/214941821-a39b9bd4-fe7c-4c0b-9070-94ce4ca73f7c.PNG)
It shows the behaviour of a 2X1 mux.
According to the above two waveforms,the waveforms of stimulated RTL Code : Is of a LATCH the waveforms of gate level netlist thruogh GLS after synthesis: Is of 2X1 MUX. It can be seen a Synthesis-Simulation Mismatch.

#### SKY130RTL D4SK3 - Labs on synth-sim mismatch for blocking statement	

	**Case 3: Wrong order of assignment in blocking assignments**
![image](https://user-images.githubusercontent.com/123365818/214943739-6f648327-7fa4-4269-acef-09e7719fd609.png)
	
module blocking_caveat (input a , input b , input c , output reg d);
	
reg x;

always @(*)
	
begin
	
	d = x & c;
	
	x = a | b;
	
end 
	
endmodule
	
![blocking_RTL](https://user-images.githubusercontent.com/123365818/214943404-6d12f6e3-3bcd-4c9c-99cd-169a916a900b.PNG)

Looking into the loop whenever any of the inputs a and b or C changes but D is assigned with old X value since it is using the value of the previous Tclk ,the simulator mimics a delay or a flop. In the synthesis, it can be seen the the OR and AND gates as expected.

![blocking_wave](https://user-images.githubusercontent.com/123365818/214944516-2d6cccf6-cc74-4e02-994f-d2c34f3f5f13.PNG)

At the instance where both the inputs a and b are 0. a | b should output 0, which when ANDed with c, should give an output y of 0. The output y thus should hold the value 0. Instead,it holds the value 1 . But due to the blocking statements in the rtl code, x actually holds a the value of a OR b from the previous clock, hence giving us an incorrect output.
	
The netlist representation on synthesis yields
	
![blocking_cre](https://user-images.githubusercontent.com/123365818/214945577-a0d539ec-ffdd-4f3b-b0db-7db7b3aba0e8.PNG)
	
The Yosys implementation show with no latch. The synthesizer does not see the sensitivity list rather the functionality of the RTL design.Hence,the netlist representation does not include any latches to hold delayed values pertaining to the previous cycle. It only includes an OR 2 AND gate.

Analyzing the design with gate level simulations on this netlist in verilog, the following waveform can be observed.
	
![Blocking_GLS_wave](https://user-images.githubusercontent.com/123365818/214948000-1139397c-0e2c-4e23-af83-111c0aaa1497.PNG

The circuit behaves as intended combinational ckt. Output d results from the present value of inputs, and not the previous clock values like in the simulation results. Since the waveforms of the stimulated RTL verilog code do not match with the gate level simulation of generated netlist,a Synthesis-Simulation Mismatch is occured.

## Day 5 - If, case, for loop and for generate

### SKY130RTL D5SK1 - If, Case constructs

**If constructs**

If condition is used to to write priority logic. The condition one has a priority or if has more priority than the consecutive else statements . Only when condition 1 is not met condition 2 is evaluated and so on and y is assigned accordingly depending on the matching conditions.

if (cond_1)

begin 

	y = statement_1;
	
end

else if (cond_2)

begin

	y = statement_2;
	
end

else if (cond_3)

begin

	y = statement_3;
	
end

else

begin

	y = statement_4;
	
end
	
![image](https://user-images.githubusercontent.com/123365818/215223187-a744a8fa-d566-4538-9922-b903fe3a6a00.png)

If-else block implements a Priority logic that is if cond_1 is satisfied, next if statements are not executed. Thus above If-Else code translates to a ladder like multiplexer structure in the final design instead of a single multiplexer.

Dangers of using Incomplete If statements Inferred Logic which occurs due to bad coding styles that is incomplete if statements.

if (condt1) 

    y-a;
    
else if (condt2)

    y=b;
    
In the above code if condition 1 is matched y is equal to a else if condition 2 is matched y is equal to b but there is no specification for the case when condition2 is not matched, as a result of which the simulator tries to latch this case to the output y. It wants to retain the value of y.
	
![image](https://user-images.githubusercontent.com/123365818/215223520-26540684-65e5-4a40-84c5-81808e0f7e75.png)

This is a combinational loop to avoid that the simulator infers a latch. Enable of this latch is OR of the condition 1 and condition 2. If neither condition 1 or condition 2 is met the OR gate output disables the latch. The latch retains the value of y and stores it.
This is called the inferred latch due to incomplete if statements which is very dangerous for RTL designing. It should be avoided except for some special cases like the counter.

reg[2:0]

always @(posedge clk,posedge reset)

Begin

If (reset)

	Count<= 3'b000;

else if(enable)

	Count<= count+1;

end

This is also a case of incomplete if statements. Here ,if there is no enable the counter should latch onto the previous value.For example if the counter has counted up till 4 and there is no enable then it should retain the value 4 rather than going to 0 again.
So here the incomplete if statements result in latching And retaining the previous value which is our desired behavior in a counter. The earlier mux example was a combinational circuit and therefore we cannot have inferred latches.
		
![image](https://user-images.githubusercontent.com/123365818/215223727-5d33ad25-e799-49a4-b664-dddc90304731.png)

Note:
* If, case statements are used inside always block.
* In verilog whatever variable we use to assign in if or case statements must be a register variable.

**CASE Constructs**

Let's look at the following verilog code block. Here, the inferred hardware should be a 4:1 multiplexer. The CASE statements do not have priority logic like IF statements.

always @(*)

begin

     case(sel)
     
		2'b00: begin
		
			y = statement_1;
			
			end
			
		2'b01: begin
		
			y = statement_2;
			
			end
			
		2'b10: begin
		
			y = statement_3;
			
			end
			
		2'b11: begin
		
			y = statement_4;
						
			end
			
	endcase
	
end

Depending on the cases matching the select y is assigned accordingly.

![image](https://user-images.githubusercontent.com/123365818/215224082-eabacfee-cc6f-4fa0-adc6-277d4585e493.png)

Some caveats with using CASE statements:

**Incomplete CASE**
Let's say we have a two bit variable select.

reg [1:0] sel

always @(*)

begin

     case(sel)
     
     2'b00: begin
     
                   . condition 1
		   
                   end
		   
     2'b01: begin
     
                   . condition 2
		   
                   end
				
    end case
    
    end
    
Then select is C1 or C3 the conditions are not specified. It causes an incomplete case which results in inferred latches for these two cases that latch on to output y.This occurs when some cases are not specified inside the CASE block.
For example, if the 2'b10 and 2'b11 cases were not mentioned , the tool would synthesize inferred latches at the 3rd and 4th inputs of the multiplexer.
		
![image](https://user-images.githubusercontent.com/123365818/215224423-90b84bfa-e2c5-4273-bb34-5e602534b458.png)

Solution is code case with default inside the CASE block so that the tool knows what to do when a case that is not specified occurs.

**Correct code :**

reg [1:0] sel

always @(*)

begin

     case(sel)
     
     2'b00: begin
     
                   . condition 1
		   
                   end
		   
     2'b01: begin
     
                   . condition 2
		   
                   end
		   
    **default :**begin
    
                   . condition 3
		   
                   end   
		   
    end case
    
    end
    
**Partial assignments**

always @(*)

begin

	case(sel)
	
		2'boo: begin
		
			x = a;
			
			y = b;
			
		2'b01: begin
		
			x = c;
			
		default: begin
		
			x = d;
			
			y = d;
			
			end
			
	endcase
	
end

In the above example, we have 2 outputs x and y. This will create two 4X1 multiplexers with the respective outputs. 
If we look at case 2'b01, we have specified the value of x for this case ,but not the value of y. It appears that it is okay to do so, as a default case is specified for both the outputs, and if we don't directly specify the value of y for any case, the simulator will implement the default case. 
This, however , is incorrect. 
In partial assignments such as this, the simulator will infer a latch at the 2nd input for multiplexer y as no value is specified for a particular case.
		
![image](https://user-images.githubusercontent.com/123365818/215224793-c7e155a5-e280-4545-b677-016224a288f1.png)
		
Note: assign all the outputs in all the segment of CASE

**Overlapping cases**

always @(*)

begin

	case(sel)
	
		2'b00: begin
		
			y = a;
			
		2'b01: begin
		
			y = b;
			
			end
			
		2'b10: begin
		
			y = c;
			
		2'b1?: begin
		
			y = d;
			
			end
			
	endcase
	
end

In the above code block ,2'b1? specifies that the corresponding bit can be either be 0 or 1. This means when the sel input is holding a value 3 i.e 2'b11, cases 3 and 4 both hold true. What is synthesized depends on the mercy of the simulator. It can lead to **Synthesis-Simulation mismatches**.
If we used an IF condition here, due to **priority logic**, condition 4 would be ignored when condition 3 is met. 
However,in the CASE statement , even if the upper case is matched,**all the cases are checked**.So,if there is overlapping in cases,it poses a problem as the cases are not mutually exclusive. And we would get an unpredictable output.

![image](https://user-images.githubusercontent.com/123365818/215225110-8d4a5844-ce9f-4297-8e4b-8c0f56e1d86d.png)

### SKY130RTL D5SK2 - Labs on "Incomplete If Case"
		
**Case 1: Incomplete If statements**

Below is the file titled incomp_if.v, and can be found in the directory verilog_files.

module incomp_if(input i0 , input i1 , input i2 , output reg y);

always @(*)

begin

	if(i0)
	
		y <= i1;
		
end

endmodule

![incomple_if_RTL](https://user-images.githubusercontent.com/123365818/215185966-2b9196a5-2674-4623-8424-4c901d193386.PNG)

The code contains an incomplete IF statement as no else condition corresponding to it is mentioned. 

![image](https://user-images.githubusercontent.com/123365818/215185863-2fd5a671-e264-4b82-b395-62dfe2666e2c.png)

On simulating this design , following gtkwave is obtained
		
![if_gtkwave](https://user-images.githubusercontent.com/123365818/215186188-bc561372-508a-43e1-ae33-54511df3d267.PNG)
		
From the above waveform, it can be observed no change in y when i0=0.
		
![ifwave](https://user-images.githubusercontent.com/123365818/215186158-09296e0f-9acc-45cc-b379-b2ff8429cdc7.PNG)
		
 It's equal to previous value when io=0. 
 This shows latching Action, which is verified by looking at the synthesis implementation using Ysosys.
		
 ![if_incom_syn](https://user-images.githubusercontent.com/123365818/215186321-1639a99d-6b62-478b-a4e6-35c6591dd2bd.PNG)
		
It can be seen a D latch is created in the synthesized netlist.

**Case 2:**

Below is a similar example of incomp_if2.v

module incomp_if2(input i0 , input i1 , input i2 , input i3,  output reg y);
		
always @(*)
		
begin
		
	if(i0)
		
		y <= i1;
	
	else if (i2)
	
		y <= i3;
		       

end
		       
endmodule
		       
The above code contains an incomplete IF statement as well. We can also see its test bench by using vim 
		       
![if2_rtl](https://user-images.githubusercontent.com/123365818/215189066-92f5c53d-af9c-4f2f-875a-b0cbd9bf3ded.PNG)

Here, there are 2 inputs i1 and i3, as well as 2 conditional inputs i0 and i2. 
As we do not specify the case when both i0 and i2 go low, which results in an issue in the synthesis.
		       
![image](https://user-images.githubusercontent.com/123365818/215189525-09be8908-d0d9-417f-81e7-036382b3702d.png)
		       
![image](https://user-images.githubusercontent.com/123365818/215192625-e18879b5-b718-4f68-8cf4-e9d7aa80ea08.png)

The gtkwaveform of the simulated design is below

![if2wave](https://user-images.githubusercontent.com/123365818/215189297-c7152986-3aee-4a05-8eaa-49ebb12060de.PNG)
		       
Analyzing the waveform, 
When io is high,output follows i1. When io is low,it looks for i2. 
If i2 is high,it follows i3. 
But if i2 is low(and io is already low),y attains a constant value=previous output.
This can also be verified by checking the graphical realisation of the yosys synthesis below.
		       
![if2syn](https://user-images.githubusercontent.com/123365818/215190210-75e9751d-4d14-4f6e-9530-658b7d524c7a.PNG)
		       
Yosys synthesizes a multiplexer as well as a latch with some combinational logic at its enable pin.

### SKY130RTL D5SK3 - Labs on "Incomplete overlapping Case"
		       
In this lab, we will study four cases.

![Case](https://user-images.githubusercontent.com/123365818/215191420-ab2836c9-8cd2-413d-ace4-91589e3382ce.PNG)

**Case 1: Incomplete Case**

A design with Incomplete Case's specification in a mux

module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel , output reg y);

always @ (*)

begin

	case(sel)
	
		2'b00: y = i0;
		
		2'b01: y = i1;
		
	endcase
	
end

endmodule
			
By using vim command,
			
![incomcasrtl](https://user-images.githubusercontent.com/123365818/215192804-8805000e-d64f-4a14-aa55-3836c3aa7e7a.PNG)

The truth table for the above 2X1 mux looks like:

sel[1]	sel[0]	y
			
0	0	io
			
0	1	i1
			
1	0	latch
			
1	1	latch
			
Whenever se[1]=1 ,latching action takes place.
			
![image](https://user-images.githubusercontent.com/123365818/215193211-1ad6cd2a-c6ae-4371-90a4-b80819211c2d.png)
			
The GTKWave,
			
![incomcasewave](https://user-images.githubusercontent.com/123365818/215194213-20493df7-4376-4ea8-a6cc-5c4d4a38340c.PNG)
			
When sel is 10 and 11, there is inferring latch.

The yosys synthesis implementation is given below.

![incomcasesyn](https://user-images.githubusercontent.com/123365818/215194911-5f7722a0-2f88-4f2b-9fd3-e9b583ce138a.PNG)
			
!(sel[1]) is going to D latch enable. 2.The inputs io,sel[0], !(sel[1]) go to the upper mixing logic that is implemented on D pin of the latch.

**Case 2: Complete Case**

Complete Mux along with all the cases specified.

module comp_case (input i0 , input i1 , input i2 , input [1:0] sel , output reg y);

always @ (*)

begin

	cae(sel)
	
		2'b00: y = i0;
		
		2'b01: y = i1;
		
		default:y = i2;
		
	endcase
	
end

endmodule
			
The RTL code by using vim
			
![complecasertl](https://user-images.githubusercontent.com/123365818/215195429-9ce8f3b1-9820-4557-8d13-654256d38101.PNG)

Output follows i2 at default case,if i1 and io go low. 
			
![image](https://user-images.githubusercontent.com/123365818/215195312-744fd609-5e91-452f-ac44-b5923f3d11d1.png)

Hence a 4X1 mux is synthesized without any latch that can be verified below.
			
![compcasesyn](https://user-images.githubusercontent.com/123365818/215196675-38262c2c-f6af-496e-be09-9685a1ec4f0d.PNG)

**Case 3: Partial Assignments**

module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel , output reg y , output reg x);

always @ (*)

begin

	cae(sel)
	
		2'b00: begin
		
			y = i0;
			
			x = i2;
			
			end
			
		2'b01: y = i1;
		
		default: begin 
		
			x = i1;
			
			y = i2;
			
			end
			
	endcase
	
end

endmodule

Aim: The 2X1 mux with output y is inferred without any latch. To find out the latching condition of the second mux we take the help of the following truth table:

sel[1]	sel[0]	x

0	0	i2

0	1	latch

1	0	i1

1	1	i1

Condition for enabling:

              en=sel[1]+!(sel[0]) 
	      

![image](https://user-images.githubusercontent.com/123365818/215197604-7115e463-3780-4872-968b-b211abf615df.png)
			
Using Redundancy Theorem

Yosys implementation of the above design after synthesis,
			
![partialcasesyn](https://user-images.githubusercontent.com/123365818/215199242-058c8b9d-7f7f-4b59-aa66-abfbf9805650.PNG)

As expected, only one D latch is inferred for X. No latch is inferred for y.

**Case 4: Overlapping Bad cases**

Design of 4X1 mux having overlapping cases

module bad_case (input i0 , input i1 , input i2 , input [1:0] sel , output reg y);

always @ (*)

begin

	cae(sel)
	
		2'b00: y = i0;
		
		2'b01: y = i1;
		
		2'b10: y = i2;
		
		2'b1?: y = i3;
		
		
	endcase
	
end

endmodule
			
By using vim command,

![bad cas-rtl](https://user-images.githubusercontent.com/123365818/215200480-c15d0bf1-bdf0-435c-ad9d-5b7643cbb06c.PNG)

In gtkwaveform of RTL simulation:
			
![badcasewave](https://user-images.githubusercontent.com/123365818/215202515-bdfaaeab-9504-4665-9371-73a036523b6c.PNG)

Yosys implementation of the above design after synthesis,
			
![bad case syn](https://user-images.githubusercontent.com/123365818/215201644-838b9ea4-5b2e-4682-b6d2-4466b6635c92.PNG)
			
Analyze the waveform: 
When sel[1:0]=11, the output neither follows i2 nor i3. 
It simply latches to 1.

Running GLS on the netlist,the waveform of the synthesized netlist behaves as 4X1 mux as shown below

![GLSwithbadcase](https://user-images.githubusercontent.com/123365818/215203465-2781cc2d-1d59-44b9-abba-db2f8887d8d4.PNG)

Overlapping cases confuse the simulator and leads to Synthesis-Simulation Mismatches.

### SKY130RTL D5SK4 - for loop and for generate
			
There are two types of FOR loops in verilog.

1.FOR loop 

	1.Used within the always block
	
	2.Used to evaluate expressions
			
2. Generate FOR loop 

	1.Only used outside the always block
	
	2.Used for instantiating hardware
			
![image](https://user-images.githubusercontent.com/123365818/215204285-67587082-dbb1-4550-ac9d-d65879cce834.png)

**Necessity of FOR loops**

**For loops** are extremely useful when we want to write a code design that involves multiple assignments or evaluations within the always block. 
For example,
If we want to write the code for 4:1 multiplexer, we can easily do so using a either four if blocks or using a case block with 4 cases,as seen in the previous if-else blocks.But this approach is not suitable for complicated design with numerous inputs/outputs say 256X1 mux.
			
![image](https://user-images.githubusercontent.com/123365818/215205019-9402cd88-6198-455f-adcb-b8fd5cb1751f.png)
			
In code point of view
			
![image](https://user-images.githubusercontent.com/123365818/215205285-96d0ba1c-d114-4202-b17a-8ff1261b7b4d.png)

If we wanted to design a 256X1 multiplexer, we will have to write 256 lines of condition statements using select and corresponding assignments. 
But in for loop,be it 4X1 or 256X1 we would always be writing 4 lines of code only. Although we need to provide 256 inputs using an internal bus.


integer k;

always @(*)

begin

	for (k = 0, k < 256, k= i  +1)
	
	begin
	
		if (k== sel)
		
			y = in[i];
			
		end
		
	end
	
end

This code can be infinitely scaled up by just replacing the condition i < 256 with the desired specification for the target multiplexer.

Similarly, it can be made High input demultiplexers as well.

integer k

always @(*)

begin

	int_bus[15:0] = 16b'0;
	
	for (k= 0; k< 16; k= k+ 1) 
	
	begin
	
		if (k == sel)
		
			int_bus[k] = inp[k];
			
		end
		
	end
	
end

Here , A 16:1 demultiplexer is made by using for loops within the always block. 
The int_bus[15:0] specifies the internal bus which takes on the input of the demux. 
It is necessary to assign all outputs to low for a new value of sel else latches will be inferred resulting in the incorrect implementation of our logic.
	
![image](https://user-images.githubusercontent.com/123365818/215206097-8dec2b83-be2e-4c8d-82fe-02c04017bb7c.png)
	
Note: **for** is inside the **always**.
	
![image](https://user-images.githubusercontent.com/123365818/215206572-ae60e3cf-55c3-45a7-a716-53f9b6303854.png)
	
Note: Replacte the Hardware, **for** is outside the **always** .
For example, 
In Ripple Carry Adder, we need to replicate the full adder.
	
![image](https://user-images.githubusercontent.com/123365818/215208756-7748f598-68ff-44b5-a499-27fb9251790f.png)
	
To summrize,
	
![image](https://user-images.githubusercontent.com/123365818/215208940-b72f9e31-3b4a-4b89-be20-dcbb06c5e3db.png)
	

### SKY130RTL D5SK - Labs on "for loop" and "for generate"

Below are few of the examples of FOR construct .

**Case 1:FOR construct**

file mux_generate.v that generates a 4X1 mux using For loop.

module mux_generate (input  i0, input i1 , input i2 , input i3 , input [1:0] sel , output reg y);

wire [3:0] i_int;

assign i_int = {i3,i2,i1,i0};

integer k;

always @(*)

begin

	for(k = 0; k < 4; k=k+1)begin
	
		if(k == sel)
		
			y = i_int[k];
			
	end
	
end

endmodule

The 4 inputs get assigned to a the internal 4 bit bus named i_int.
![muxgeneratertl](https://user-images.githubusercontent.com/123365818/215210708-179ca342-cd58-4992-84d9-2596acb103bb.PNG)
![image](https://user-images.githubusercontent.com/123365818/215211114-c80808dc-424e-4bbf-9abc-ff78dbb3977d.png)


The gtkwave obtained after the simulation
			 
![muxgenerwave](https://user-images.githubusercontent.com/123365818/215212674-bbe56678-42dd-4f86-a0e3-7d758bb0e4b4.PNG)

Yosys implementation of the above design after synthesis
			 
![muxgeneratesyn](https://user-images.githubusercontent.com/123365818/215219740-0e0ed9bf-6502-4839-be94-5982b4c0a98c.PNG)

While running GLS on the netlist,the waveform of the synthesized netlist is the same with the RTL simulation
			 
![mux_gene_gls](https://user-images.githubusercontent.com/123365818/215221538-f9922523-4d36-42da-ada1-7f99a542567e.PNG)
 

**Case 2: Blocking** 

Similar to case 1, file demux_generate.v that generates a 4X1 demux using For loop.

module demux_generate (output o0 , output 02 , output o3 ,  output o4 , output o5 , output o6 , output o7 , input [2:0] sel , input i);

reg [7:0]y_int;

assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;

integer k;


always @(*)

begin

	y_int = 8'bo;
	
	for( k = 0; k < 8; k++) begin
	

		if(k == sel)
		
			y_int[k] = i;
			
	end
	
end 

endmodule

![demux_rtl](https://user-images.githubusercontent.com/123365818/215215171-39d04429-949d-4cf5-ace5-4df909cca450.PNG)

The above code has good readabilty,scalability and easy to write as well. Let's verify if it functions as a 8X1 demux as expected by viewing its gtkwave simulated waveform.
			 
![demux_wave](https://user-images.githubusercontent.com/123365818/215214750-ed96ab3c-e44a-41b3-994a-82a3b7bdfb58.PNG)

Yosys implementation of the above design after synthesis

![demux-syn](https://user-images.githubusercontent.com/123365818/215220248-250f3f9b-c41e-4950-b928-8cf395e5f75e.PNG)

While running GLS on the netlist,the waveform of the synthesized netlist is the same with the RTL simulation
			 
![demu-gls](https://user-images.githubusercontent.com/123365818/215222196-65b41b72-bee7-47b3-b52a-eb6a3b391230.PNG)


**Case 3: 8 bit Ripple Carry Adder with replicating**

**FOR Generate and its Uses**

			 RCA
			 
![image](https://user-images.githubusercontent.com/123365818/215216601-7f0730da-34a5-4d9a-93a0-6342829e8e92.png)
One way
			 
![image](https://user-images.githubusercontent.com/123365818/215216995-080384dd-c5fc-4c92-8bc1-d81951535eee.png)
Another way
			 
![image](https://user-images.githubusercontent.com/123365818/215217319-9b053165-6433-4c1b-b793-ab43300c3742.png)

**FOR Generate** is used when we need to create multiple instances of the same hardware. We must use the For generate outside the always block.
We take example of a 8 bit Ripple Carry Adder to understand the ease of instantiations provided by the For generate statement.
A Ripple Carry Adder consists of Full Adders tied in series where the carry out of the previous full adder is fed as the carry in bit of the next full adder in the chain. 
Hence, we can make use of generate for to instantiate every full adder in the design , as they are all represent the same hardware.

For this example , we use the file rcs.v which holds the code for the ripple carry adder. It also needs to be included in our simulation.

module rca (input [7:0] num1 , input  [7:0] num2 , output [8:0] sum);

wire [7:0] int_sum;

wire [7:0] int_co;


genvar i;

generate

	for (i = 1; i < 8; i=i+1) 
	begin
	
		fa u_fa_1 (.a(num1[i]),.b(num2[i],.c(int_co[i-1],.co(int_co[i]),.sum(int_sum[i]));
		
	end
	

endgenerate

fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));


assign sum[7:0] = int_sum;

assign sum[8] = int_co[7];

endmodule

Here, fa references another verilog design file containing the definition of for the full adder submodules .This is shown below, from the fa.v file

module fa(input a, input b , input c,  output co, output sum);

	assign  {co,sum} = a +b + c;
	
endmodule

In the RCA verilog code, we instantiate fa in a loop using generate for outside the always block.

Rules for addition :

N + N bit number --> Sum will be N + 1 bits N +M bit number --> Sum will be max(N,M) +1 bits

Now, let us simulate this design in verilog and view its waveform with GKTWave.
As the rca design referances the file fa.v , we must specify it in our commands as follows

**iverilog fa.v rca.v tb_rca.v**

./a.out

gtkwave tb_rca.v

The resulting gtkwaveform is shown below that shows an adder being simulated:

![rcawave](https://user-images.githubusercontent.com/123365818/215218603-c1455913-70a5-472a-9eec-22f85bb4fa58.PNG)

Yosys implementation of the above design after synthesis

![rcasyn](https://user-images.githubusercontent.com/123365818/215220734-963d766e-9d02-422c-89fb-234cb434a219.PNG)

While running GLS on the netlist,the waveform of the synthesized netlist is the same with the RTL simulation

![rcagls](https://user-images.githubusercontent.com/123365818/215222667-d40d2bb3-6e6d-4681-a13e-807dc6b792a7.PNG)

**Takeaways from this workshop:**

* Learning how to write the verilog codes using for, for generate,if-else, case ,blocking/non blocking assignments so that our intended functionality is met.

* Learning to genertaed the gtk waveform ans see the simulated wave results. We learnt to read timing diagrams as well

* Learning to synthesize the rtl verilog design using yosys and understand how the synthesizer implements the logic considering area and delay optimisations

* Learning to verify the simulation -synthesis results by feeding netlist+testbench+Gate verilog models to the iverilog,during GLS(gate level synthesis simulation)

* Learning good coding practices and ways to write optimised verilog codes

**Acknowledgment:**

1.Kunal Ghosh - Co-founder(Vsd corp. pvt.ltd.)

## Day 6
### FPGA_D1SK1 - FPGA introduction

What Is FPGA And FPGA Architecture?
FPGA vs ASIC

![image](https://user-images.githubusercontent.com/123365818/215936708-24cb884a-f9d9-400e-a689-b910dbbf236a.png)

FPGA Architecture
![image](https://user-images.githubusercontent.com/123365818/215936823-d62af9da-02b9-4652-bd05-68b7b4f5a271.png)

Lookup Table
![image](https://user-images.githubusercontent.com/123365818/215937009-1590770c-9cef-41e4-9142-08b4ffe7fd1d.png)

![image](https://user-images.githubusercontent.com/123365818/215937409-b211d6ad-b16e-46e3-a295-0d98152a06d0.png)

![image](https://user-images.githubusercontent.com/123365818/215937531-8041ecb0-feb5-4725-b7f4-050809fb0293.png)

### FPGA_D1SK2_Vivado-counter
1. Create Project in Vivado
![image](https://user-images.githubusercontent.com/123365818/215939049-c385da41-e343-4894-a922-c61dad47c29f.png)

	Add Source for design file

	![image](https://user-images.githubusercontent.com/123365818/215939214-6b9b9ecc-81bb-4347-92c6-767f42686a9b.png)
	
	Design Verilog file
	![image](https://user-images.githubusercontent.com/123365818/215940664-8aa683a1-575d-4227-86c7-00244472da05.png)

	
	Add simulation for test file
	![image](https://user-images.githubusercontent.com/123365818/215939333-da1faa05-23ef-4bc6-802e-eff0b6a871cc.png)



2. Run Simulation
	Run Behaviour Simulation
	![image](https://user-images.githubusercontent.com/123365818/215939420-9916abf6-1119-4a2d-b887-226cc0880f99.png)

	![image](https://user-images.githubusercontent.com/123365818/215939501-53322a63-c2c0-4e87-8166-48f5c0ce6d4b.png)

![image](https://user-images.githubusercontent.com/123365818/215938258-7305001d-062d-4699-846a-1a575d5c9119.png)
Time waveform
![image](https://user-images.githubusercontent.com/123365818/215938321-ce1089aa-49a3-4e21-8a25-156e4bd29104.png)

3. Elaboration
	Open Elaboration Design
	![image](https://user-images.githubusercontent.com/123365818/215940305-46d7799e-90a6-46f7-961a-6c1231d39146.png)

	Schematic
	![image](https://user-images.githubusercontent.com/123365818/215938430-1f85f4a0-e701-4aa6-84b6-73bedf2cc7a6.png)
	
	IO Floor Planing
	
	![image](https://user-images.githubusercontent.com/123365818/215940455-5c6853f6-8766-460b-bb97-150a7e55a17d.png)
	
	Pin mapping and Voltage Specification
	Vcc =3.3V
	![image](https://user-images.githubusercontent.com/123365818/215941348-dac72335-c5c3-4753-8e9a-2b290e9800cf.png)
4. Run Synthesis
	![image](https://user-images.githubusercontent.com/123365818/215941482-797c8e10-9288-44a5-a599-1773ddca27d6.png)

	Report timing summry
	![image](https://user-images.githubusercontent.com/123365818/215941623-938f845d-385b-462b-ae8b-2305ab9c08c2.png)

	There are no user specified timing constraints.
5. Add Constraints
	![image](https://user-images.githubusercontent.com/123365818/215944184-a48cf0af-dbef-4e90-a6fb-093ddb97a7ca.png)

	
 Constraint wizard,
	![image](https://user-images.githubusercontent.com/123365818/215944335-055bacc3-c946-476c-89eb-d928104a7167.png)
	
	![image](https://user-images.githubusercontent.com/123365818/215944511-30372fa8-16e6-4de1-baef-cc117509c957.png)

	There is no clk information to select.
 Edit Timing Constraints, create clock with 10 ns.
	![image](https://user-images.githubusercontent.com/123365818/215944835-cebf1e67-0232-4f3b-9ee8-ea2ec22ba925.png)
	
Schematic is also not correct
	![image](https://user-images.githubusercontent.com/123365818/215945042-04089e49-2a2c-4129-926d-f0c75280d343.png)
	
	![image](https://user-images.githubusercontent.com/123365818/216298014-1f342617-912a-46e6-90ba-ae1ee92d124f.png)

	
	
	
	
6. Generate Bit Stream
	![image](https://user-images.githubusercontent.com/123365818/215945305-bfedff9b-82d8-41a4-a88d-47ae0bc2e8ba.png)
Open hardware manager
	![image](https://user-images.githubusercontent.com/123365818/215945487-1469692c-c8e4-41e3-8afc-794f8dffbc38.png)

	Open target, connect auto
![image](https://user-images.githubusercontent.com/123365818/215945764-1680b3a7-4d6e-47f5-9ecd-196c0f54fa3a.png)
Open implemented Design
	![image](https://user-images.githubusercontent.com/123365818/215945980-db625141-2bc1-46b9-be1c-6d1d4e525e85.png)
	
7. Run Implemetation

	![image](https://user-images.githubusercontent.com/123365818/215947241-6c1620de-dab0-4579-a116-6f3d1b342ab6.png)

Appear warning message
	![image](https://user-images.githubusercontent.com/123365818/215947473-5799441f-2198-430f-a06a-b86dcc948442.png)
	
![image](https://user-images.githubusercontent.com/123365818/215947531-2ee5c370-efe6-471e-8c20-0dddf340f01c.png)
	
![image](https://user-images.githubusercontent.com/123365818/215947603-b792a5ed-c99c-4b97-95e2-fb5b305a909d.png)
	
	Error is occured
	
	
![image](https://user-images.githubusercontent.com/123365818/216298131-735085c9-2f02-40de-951a-f222e402d03d.png)

![image](https://user-images.githubusercontent.com/123365818/216330968-d3208044-c926-457c-97f9-b34a46388cfb.png)

## Day 7
### SKY130_D1_SK1 - How to talk to computers

The computers can perform the intruction, which are given externaly and some hardware is there which convert the external instruction into understandable language of computers. For example FPGA board, Arduino board etc. are provides the medium for external inputs and outputs.
FPGA Board in Embedded System, etc.
Example Board is 
	
![image](https://user-images.githubusercontent.com/123365818/215948656-82e2d02f-4aba-406b-a0eb-ddb0d8809bc6.png)
	
Arduino consists of both a physical programmable circuit board (often referred to as a microcontroller) and a piece of software, or IDE (Integrated Development Environment) that runs on the computer, used to write and upload computer code to the physical board.
Basically microcontroller is made from package, inside the package chip is there.Chip is made by pads, core, die and foundary IP's.
	
	VCC, GND , SDRAM . etc. are inside the chip on the board
	
![image](https://user-images.githubusercontent.com/123365818/216331554-9bd595aa-d794-4691-b242-72beca0c87d6.png)

	
	
**Basic Terms:**
	**Packages **
	The package is a case that surrounds the circuit material to protect it from corrosion or physical damage and allow mounting of the electrical contacts connecting it to the printed circuit board (PCB). here what we are seeing in the black color is the Package of the microcontroller.
	
	Pin location
	
![image](https://user-images.githubusercontent.com/123365818/216331498-49a65676-4412-4069-b4ee-566942d7b290.png)


	Chip in the center of package
	Inside the Package, chip is available which contains, foundary IP's (for example, RISC-V Soc, PLL, ADC, DAC, SRAM, SPD), core (core contains AND, OR etc. all gates and digital logics), pads (through which input and output signals are communicates).


   **Wire bonding**
	
![image](https://user-images.githubusercontent.com/123365818/216331386-57f4ff1e-0d82-4b39-a2ab-e6b03b5dd6ac.png)

	
	Look Inside the chip , pads, die and core can be found
	
**Pads:** signal can go inside the chip and outside of the chip through the pads
	
**Core:** digital logics are placed in the area
	
**Die:** manufacturing on the silicon wafer 
	
![image](https://user-images.githubusercontent.com/123365818/216331658-17a47c88-257f-42c3-bd76-4d75ccf09e79.png)
	
	ADC, DAC, SRAM,PLL are called as Foundry IP’s.
	
Digital Block such as SPI and SOC are called as **Macro** (pure digial logic)
	
![image](https://user-images.githubusercontent.com/123365818/216331704-dcfc9ecf-a86b-4fb5-ae44-8e6d0ee2f2da.png)
	
	
**Foundry IP**: all Intellectual Property, whether Background IP or Foreground IP, regardless of when or for what purpose it is developed, pertaining to genetic components, pathways, and strains; and methods and tools for design, genetic engineering, testing and/or small-scale fermentation of microbial strains. Notwithstanding the foregoing, this category shall exclude Ginkgo Background IP, Collaboration Strain and Process IP, and Foreground Application IP.

### SKY_L2 - Introduction to RISC-V

	Talk to computer

**ISA (instruction set architecture)**
RISC-V, where five refers to the number of generations of RISC architecture that were developed at the University of California, Berkeley. RISC is an open standard instruction set architecture (ISA) based on established RISC principles. Unlike most other ISA designs, RISC-V is provided under open source licenses that do not require fees to use. A number of companies are offering or have announced RISC-V hardware, open source operating systems with RISC-V support are available, and the instruction set is supported in several popular software toolchains.

The instruction set is designed for a wide range of uses. The base instruction set has a fixed length of 32-bit naturally aligned instructions, and the ISA supports variable length extensions where each instruction can be any number of 16-bit parcels in length. The instruction set specification defines 32-bit and 64-bit address space variants. The specification includes a description of a 128-bit flat address space variant, as an extrapolation of 32 and 64 bit variants, but the 128-bit ISA remains "not frozen" intentionally, because there is yet so little practical experience with such large memory systems.
	
![image](https://user-images.githubusercontent.com/123365818/215950361-185a069d-950b-4874-aec6-5da7c2dcad54.png)

	C program to assembly language program (hex format or binary format) map  particular layout.

	Implementation in RTL 
	
![image](https://user-images.githubusercontent.com/123365818/215950696-dc6762a2-9fdd-4d39-b1ab-a052fc4cd9d6.png)

### SKY_L3 - From Software Applications to Hardware

**Application software to Hardware by using ISA**
	
	Between apps or software (in our mobilephones and computers or laptops) and Hardware (in our mobilephones and computers or laptops), One full channel is there, which contains O.S. (operating system), compiler and assembler. This channel proccesses the inputs from the apps and gives the outputs to the Hardware. And according to the output of assembler, the hardware will perform.

![image](https://user-images.githubusercontent.com/123365818/215951076-eccf91cf-8180-49e8-bb44-4a0598340eda.png)

	Application Software to System Software to Hardware
	Major components of system software : OS, Compiler, Assembler
	OS: Handle IO operations
	    Allocate memory
            Low level system functions
	
When the inputs is given from the software, inputs is in the C,C++,JAVA etc. languages. Hardware not understand this language. So, this inputs goes throuth the system software cahnnel, where O.S. handles the input/output operations, it allocates the memory to for the codes and low level systems functions are there in O.S. Then program goes to the compiler, where program will compile and generate the HDL program.This HDL program is basically the sets of Instructions which can hardware is able to understand. The instructions are basically depends on what kind of hardware we are using. For example if we are using Mips processor then instruction formaate will be according to the Mips processor, if Hardware is RISC-V then the instruction formate will be according to the RISC-V and similerly for Arm and intel processors also.

This sets of instrusctions is now given to the Assembler. here according to the instruction set, assembler will generate the Binary code (contains only 0s and 1s). this binary code can be understandable for hardware. And according to this code, hardware will gives the output.
	
![image](https://user-images.githubusercontent.com/123365818/215951335-c3f0bf51-6179-4837-82c5-3001d2c509a9.png)
	
![image](https://user-images.githubusercontent.com/123365818/215951393-748d0a2f-877e-4aed-b101-be493de83d06.png)
	
#### SoC Design using Openlane
	
To design Digital ASIC, few tools or things which are required from the day one. These are

RTL Design
EDA tools
PDK data
	
![image](https://user-images.githubusercontent.com/123365818/215955732-7c2649e9-3005-44ae-ad5a-114ed08c0b29.png)

1. RTL Designs: In digital circuit design, register-transfer level (RTL) is a design abstraction which models a synchronous digital circuit in terms of the flow of digital signals (data) between hardware registers, and the logical operations performed on those signals.for this designs many open sorces are available. like, librecores.org, opencores.org, github.com, etc...
	
	Librecores.org
	
	Opencores.org
	
	Github.com
	
2. EDA Tools : The term Electronic Design Automation (EDA) refers to the tools that are used to design and verify integrated circuits (ICs), printed circuit boards (PCBs), and electronic systems, in general. many open sorces tools are available like Qflow, OpenROAD, OpenLANE, etc...
	
	Qflow
	
	OpenROAD
	
	OpenLANE
3. PDK :PDK is process design kit. It is interface between FAB and design. 
	
See Pure Play Fabs and Fabless design companies
	
![image](https://user-images.githubusercontent.com/123365818/215955099-250dd64f-c082-4e92-9053-f1b107559892.png)

	
PDK = the interface between the FAB and the designers
	
PDK=Process Design Kit
	
	Collection of files used to model a fabrication process for the EDA tools used to design an IC
This data is collections of files like,
	
	* Process Design Rules: DRC, LVS, PEX
	
	* Device Models
	
	* Digital standard Cell Libraries
	
	* I/O Libraries
	which are used to model a fabrication process for the EDA tools used to design an ICs. for example, in 2020, google release the open source PDK for FOSS 130nm production with the skywater technology. But right now it is at cutting age of the 5 nm also. But in many applications, the advance node is not required, and the cost of advanced node is also high as compared to 130nm processors. This 130nm processors are also fast processor. for example,
intel: P4EE @3.46 GHz(Q4'o4)
sky130_OSU (single cycle RV32i CPU) pipeline version can achieve more than 1 GHz clock
	
	
#### SKY_L2 - Simplified RTL2GDS flow
	
	

**Simplified RTL to GDSII Flow**
	
	
	Synthesis 
	
	Floor/Power Planning
	
	Placement
	
	Clock Tree Sysnthesis
	
	Routing 
	
	Sign Off
	
![image](https://user-images.githubusercontent.com/123365818/215955821-fa250fa4-17c9-4e18-ad46-a05a76027c6f.png)
	
**Synthesis**
	
Converts RTL to a circuit out of components from the standard cell library (SCL)

	Synthesis In the synthesis, the design RTL is translated to a circuit out from the SCL. 

	The resultant circuit is describes in HDL and usualy refered to the gate level netlist. 
	
	The gate level netlist is functionaly equivelent to the RTL.
	
	"standard Cells" have regular layouts.
	
![image](https://user-images.githubusercontent.com/123365818/215956556-26e16513-ffed-4932-9634-1df362fa3af4.png)

Standard Cell have regular layout

	Each has different views/models

	Electrical,HDL,SPICE

	Layout(Abstract and Detailed)
	
![image](https://user-images.githubusercontent.com/123365818/215956668-36826493-8eed-4dda-97c7-aeca3fc45fc1.png)
	
**Floor and Power Planning**

The main objective here is that to plan silicon area and distribute the power to the whole circuit. In the chip floor planning, the partition chip die between different system building blocks and place the i/o pads. In micro floor planning, we define the dimensions, pin locations, rows.
	
	**Chip Floor Planning:** Partition the chip die between different system building blocks and place the I/O Pads
	
![image](https://user-images.githubusercontent.com/123365818/215957005-9d814e3c-3911-4e8a-b2d5-31cbcabee6a6.png)
	
	**Macro floor Panning:** Dimensions, pin locations, row definition
	
![image](https://user-images.githubusercontent.com/123365818/215957152-91fbbdc8-b710-4b17-9e6e-f3f4583734b6.png)
  
	**Power Planning**
	
	In power planning, the power network is connstructed. tipically, the chip is power by multiple VDD and GND. so, total components are connected to power supply horizontaly and vertically by metal streps. here parallel structures are used to reduce the resistance. To address the electromagnetization problem, power distribution network uses upper metal leyers, which are thicker than lower metal layers. Hence have less resistance.
	
![image](https://user-images.githubusercontent.com/123365818/215957377-3b962e4a-052c-4170-ae43-7fae90e60719.png)
	
	**Placement:** Place the cells on the floorplan rows, aligned with the sites
	
![image](https://user-images.githubusercontent.com/123365818/215957542-2131deca-7ae1-4647-a43b-dfa04478e423.png)
	
	In this process, we place the gate level netlist on the floor planning rows, alligned with the sites. cells should be placed very closed to eachother to reduce the interconnnect delay. 
	
	Usually done in two steps: Global and Detailed
	
![image](https://user-images.githubusercontent.com/123365818/215958746-8db624eb-4887-4c06-8b96-4b8557548038.png)

	**Global placement** is very first stage of the placement where cells are placed inside the core area for the first time looking at the timing and congestion. Global Placement aims at generating a rough placement solution that may violate some placement constraints while maintaining a global view of the whole Netlist.
	
	In **detailed placements**,  the exact route and layers are determined for each netlist. the objective of detailed placement is valid routing, minimize area and meet timing constrains. Additional objective is minimum via and less power.

**Clock tree Synthesis**

Create a clock distribution network

	To deliver the clock to all sequential elements (e.g., FF)

	With minimum skew (zero is hard to achieve)

	And in a good shape

	Usually a Tree (H,X,..)
	
![image](https://user-images.githubusercontent.com/123365818/215958873-0ddec3a8-f6aa-45dd-b579-31ca65289f72.png)
	
Before routing the signals, we have to route the clock. In the process of clock synthesis, we have distribute the clock to the every sequential elements. for example flipflops, registers, ADC, DAC ete. basically clock netwroks looks likes a tree. where the clock source is roots and the clock elements are end leaves. Synthesization should be done in a manner that with minimum skew and in a good shape.To minimize the clock skew by using the low-skew global routing resources for clock signals.Microsemi devices provide various types of global routing resources that significantly reduce skew.Usually a tree is a H tree, X tree etc.
	
**Routing : ** Implement the interconnect using the available metal layers.
	
	After routing the clock, the signal routing comes. Making physical connections between signal pins using metal layers are called Routing. Routing is the stage after CTS and optimization where exact paths for the interconnection of standard cells and macros and I/O pins are determined. There are two types of nets in VLSI systems that need special attention in routing:


	Clock nets

	Power/Ground nets

The sky130 PDK defines the 6 routing leyers. the lowest leyer is called local interconnect layer (titanium nitride layer). Other five layers are alluminium layers.
	
![image](https://user-images.githubusercontent.com/123365818/215958966-0d1ca977-c966-428b-bce3-c097cdf2871a.png)

	* Metal tracks from a routing grid

	* Routing grid is huge

	* Divide and Conquer

		Global Routing: Generates routing guides

		Detailed Routing: Uses the routing guides to implement the actual wiring
In the proccess of routing, metal trackes forms a routing grids and these grids are huge. So, devide and conquer approach is use for routing. 
	
**Sign Off**
Once the routing is done, we can construct the final layout. This final layout will goes under the verification. Two types of verifications are there:
	
	* Physical Verifications : Here design rule checking will done and it will check the final layout and owners layout

		* Design Rules Checking (DRC)

		* Layout vs. Schematic (LVS)

	* Timing Verification : Here Static Timing Analysis will done

		* Static Timing Analysis (STA)
	

### SKY_L3 - Introduction to OpenLANE and Strive chipsets

**Problem**

	The Problem is tougher when using Open Source EDA

	* Tools Qualification?

	* Tools Calibration?

	* Missing Tools?

**OpenLANE**
Started as an Open-Source Flow for a True Open Source Tape-out Experiment
	
StriVe is a family of open everything SoCs 
	
	Open PDK, Open EDA, Open RTL
	
	OPENLANE is an automated RTL to GDSII flow that is composed of several tools such as OpenROAD, Yosys, Magic, Netgen, Fault, CVC SPEF-Extractor, CU-GR, Klayout and a number of scripts used for design exploration and optimization. It is started as an Open-source flow for a true Open Source tape-out Experiment. 
	striVe is a family of open everything SoCs:


	* Open PDK

	* Open EDA

	* Open RTL

	
![image](https://user-images.githubusercontent.com/123365818/215960300-7c212c71-8332-41b0-ab3c-82ecd7c58db8.png)
	
striVe SoC Family
	
![image](https://user-images.githubusercontent.com/123365818/215960416-08c4ee0b-5e27-4f6a-9826-8554a6ed6612.png)
	
**Main Goal:**

The main goal of OPENLANE is to produce a clean GDSII with no human intervation (no-human-in-the-loop). 
	
	**Produce a clean GDSII with no human intervention (no-human-in-the-loop)**

	Clean means:

	* No LVD Violation

	* No DRC Violations

	* Timing Violations
	
	Tuned for SkyWater 130nm Open PDK

	Containerized

	* Functionl out of the box

	* Instructions to build and run natively will follow
	
OPENLANE is tuned for skyWter130nm open PDK. it can be used to harden Macros and chips.there is two mode of operation

	**Autonomus :** it is the push botton flow. with the push botton , it is a some time base design and due to this push botton, we get final GDSII

	**interactive :** here we can run comamds and steps one by one.

It has large number of design examples(43 designs with their best configurations).
	
###  Introduction to OpenLANE detailed ASIC design flow
	
![image](https://user-images.githubusercontent.com/123365818/216004054-b8001334-ed60-47d9-909f-41c504fcd6e1.png)

The design exploration utility is also used for regression testing(CI). we run OpenLANE on ~ 70 designs and compare the results to the best known ones.
	
	DFT(Design for Test)
it perform scan inserption, automatic test pattern generation, Test patterns compaction, Fault coverage, Fault simulation.
	After that physical implementation is done by OpenROAD app. physical implementation involves the several steps:


	Floor/Power Planning

	End Decoupling Capacitors and Tap cells insertion

	Placements: Global and Detailed

	Post Placement Optimization

	Clock Tree synthesis (CTS)

	Routing: Global and Detailed

	Every time the netlist is modified.(CTS modifies the netlist and Post Placements optimization also modifies the netlist).so for that verification must be performed. The LCE(yosys) is used to formally confirm that the function did not change after modifying the netlist. ### Dealing with antenna rules Violation: when a metal wire segment is fabricated, it can act as antenna.as an antenna, it collect charges which can demaged the transister gates during the fabrication.
	
	Static Timing analysis(STA)
	
It involves the interconnect RC Extraction(DEF2SPEF) from the routed layout, followed by STA on OpenSTA(OpenROAD) tool. resulting report will shows the timing violations if any violations is there.

Physical Verification (DRC and LVS)
	
Magic is used for design Rules checking and SPICE Extraction from Layout. Magic and Netgen are used for LVS.

### SKY130_D1_SK3 - Get familiar to open-source EDA tools

#### SKY_L1 - OpenLANE Directory structure in detail

**cd command**
	
cd means change directory. this command will help us to go inside the directory.
	
**ls command**
	
ls means listing the directory. It is used to find the list of total details of directory.
	
**ls --ltr**
	
This command will help to list the details in cronological order.
	
       cd Desktop
	
	cd work/tools
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/216004162-93652469-0c44-461d-9f10-25f6e571ffc9.png)


	cd openlane_working dir
	
	cd pdks
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215980582-68a8c5ce-706d-49ca-876c-4b22506b4e12.png)
	
	cd sky130A
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215981089-2748cca8-587e-42b2-b757-dc38c41a6cd3.png)
	
	libs.ref ---specific to technology
	
![image](https://user-images.githubusercontent.com/123365818/215981550-62b3f4e7-30ea-4ba5-9160-57b581a0bf09.png)

	
	libs.tech --specific to tool
	
![image](https://user-images.githubusercontent.com/123365818/215981821-4e0b9509-4176-4e33-8162-c6b79688a140.png)
	
	cd sky130_fd_sc_hd
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215982319-ac1fd81f-f3a6-49a7-846f-27137e891b80.png)
	
Here we are working in Sky130_fd_sc_hd PDK varient. where, "sky130" is process name or node name."fd" is a foundary name (skyWater foundary)."sc" means standerd cell librery files and the last one "hd" stands for high density(basically one type of varient).
	
	cd lib
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215982743-2b7a50e3-9ea2-4790-8855-04ed412672e8.png)
	
	cd ..
	cd lef
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215983086-ffa8e288-6eee-4750-9fc0-a1b771b9bae5.png)
	
	tlef is technology lef
	
	cd ..
	cd ..
	cd ..
	cd  openlane
	
![image](https://user-images.githubusercontent.com/123365818/215983750-7e7f54b9-8a8e-4df8-99b9-16e23a1b0291.png)
	
Sky130_fd_sc_hd varient contains many technology files like verilog, spice, techlef, meglef,mag,gds,cdl,lib,lef,etc. (techlef file contains the layer information).
	
#### SKY_L2 - Design Preparation Step
	
when we enter in the OpenLANE, we have to use flow.tcl because as a name says, it will goes with the flow using the script. And by using interactive switch, we will do step by step process. without interactive switch, it will run complete flow from RTL to GDSII. 
	
go to bash shell

	**docker**
	
![image](https://user-images.githubusercontent.com/123365818/215984216-cde75248-5d1a-4759-a179-e5b8db9a5307.png)
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215984582-80da5808-1a5f-479d-9ed8-8fa38ff19f44.png)
	
	**./flow.tcl -interactive**
	
![image](https://user-images.githubusercontent.com/123365818/215985044-1da04e4a-5648-45a4-80b1-90e91c9ed66e.png)

Now OpenLANE is open and we can see that prompt will change now.
	**changing promt to percentage symbol %**
	
Required to input the package
	**package require openlane 0.9**

Now we have to input all the packages which required to run the flow. here we are ready to execute the command.
	
![image](https://user-images.githubusercontent.com/123365818/215985890-b8198df9-15b2-4805-b095-ed48b914c143.png)
	
	Look into the design flolder
	
![image](https://user-images.githubusercontent.com/123365818/215986426-3662ae6d-bc47-49c9-a6ac-e9e10558cfdc.png)
	
	cd picorva32a
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215986711-71426a30-2217-43e2-b9e0-bd0cc5079f5e.png)
	
	src file : source file
	
	tcl file
	
	config.tcl
	
	Now, if we are going into the design folder in openlane, there are nearly 30-40 designs are already builted. Out of them we can open any of the design. for example, here we are opening the picorv32a.v design. In this design we can see many files are available. i.e., scr, config.tcl, etc. This config.tlc file contains every details about the design. for example, details about enrollment, clock period, clock period port etc.
	
looking into configuration file
	**less config.tcl**
	
![image](https://user-images.githubusercontent.com/123365818/215987238-86dd541c-8fc1-4abe-997b-255bbc1732b0.png)
	
	Here we can see that the time period is set to the 5.00 nsec. but is we see in the openlane sky130_fd_sc_hd folder, the period is set about 24 nsec. so it is not override to the main file. If it override then give first priority to the main folder.
	
	20 ns clock period, etc are in the config file 

	And then go to bash shell
	Now, in openlane, we are going to run the synthesis, but before synthesis, we have to prepare design setup stage. for that command is " prep -design picorv32a".
	
	**prep -design picorv32a**
	
mergelef.py: Merging LEFs
	
![image](https://user-images.githubusercontent.com/123365818/215988574-f4d3c42e-4694-40a5-935f-674b74763c56.png)
	
so, here it is shown that preparation is completed.
	
#### SKY_L3 - Review files after design prep and run synthesis
	
After completing the preparation, in the picorv32a file, the run terictory is created. 
	
	**cd runs**
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215989065-d72c517c-d9a0-4bd5-995a-72c44f4955e9.png)
	
Inside the folder, Today's date is created. so in this terictory some folders are available which is required for openlane.
	
	**cd 01.02_08.19 ( today)**
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215989310-fbc9ff9a-0b61-4c3f-a7bf-dba3796d83ce.png)
	
In the temp file, merged.lef file is available which was created in preparation time. if we open this merged.lef file, we get all the wire or layer level and cell level information.
	
	**cd tmp**
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215989833-26d87d1a-a54e-422a-be2d-f38d69920777.png)
	
	less merged.ref
	
![image](https://user-images.githubusercontent.com/123365818/215990017-c15e3022-ae2c-4aa0-b08d-ec4b542f04e8.png)

While, in the result folder is empty because till we have not run anything and in the report folder all the folders are there about synthesis, placement, floorplanning,cts,routing,magic,lvs.
	
	**cd results**
	
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/215992243-11c80cc4-782f-48a8-b6d7-61e3df11411e.png)
	
	**cd reports**
	
	ls -ltr

![image](https://user-images.githubusercontent.com/123365818/215990668-2b7bbf25-5b98-4acc-9503-7bf0ce64958a.png)
	
Now here also one config.tcl file is available similar like design folder. But this config.tcl file contains all default parameter taken by the run.
	
	**less config.tcl**
	
![image](https://user-images.githubusercontent.com/123365818/215997708-dbabaea5-6eaf-4127-8e69-3a591e34634e.png)
	
when we make some change in the origional configuration and then we run, for example if we make a change in core utilization in the floorplanning and then we run the floorplanning, at this time in the congig.tcl file, the core utility will change and by cross checking it we can check that the modification is reflected in the exicution or not.

Now, cmds.log file takes all the record of the commands, what we have fab.
	
	pdk information
	min max library info
	
	to look the command file
	**less cmds.log**
	
![image](https://user-images.githubusercontent.com/123365818/216001600-3127261c-b967-4ec2-ac8e-d1d3edba8d7d.png)
	
	Coming back to the openlane
	after preparation complete
	we can run the synthesis
Now coming to the openlane, we are going to run the synthesis. It will take some 3-4 mnts to run the synthesis and finally synthesis will complited.
	
	**run_synthesis**
	
	It take 2 to 3 minutes
	
![image](https://user-images.githubusercontent.com/123365818/216002594-af4cc725-e46c-4d6e-abb1-17ca2fcfab08.png)
	
	Successfully synthesis
	
![image](https://user-images.githubusercontent.com/123365818/216004700-cf966074-05c8-4cfd-a4bc-5cf4dc285a73.png)

Before run, we saw that the result folder is empty. but now, after running the synthesis, we can see that all the mapping have been done by ABC.
	
![image](https://user-images.githubusercontent.com/123365818/216371442-f953af10-a68c-466d-bcc9-3bcaef94501c.png)

	And in the report, we can see when the actual synthesis has done. and the actual statistics synthesis report is showing below, which is same as what we have seen before.
	
	cd results
	
	cd synthesis
	
	ls
	
less picorv32a.synthesis.v
	
![image](https://user-images.githubusercontent.com/123365818/216372361-bcac3c83-ecad-4e92-9f8e-e6491cf5ed9d.png)
	
less merged_unpadded.lef
	
![image](https://user-images.githubusercontent.com/123365818/216375660-da546ce9-70b2-42e6-8a62-6b1be13ee28b.png)



	
##Day 8
	
##Sky130 Day 2 - Good floorplan vs bad floorplan and introduction to library cells
	
### SKY130_D2_SK2 -Chip floorplanning considerations
	
####SKY_L1 - Utilization factor and aspect ratio
	
	**Defining the width and height of core and Die**
	
![image](https://user-images.githubusercontent.com/123365818/216350712-db0ddcf2-dd6f-4b32-9cb1-94f7e94716f2.png)

	let's begin with netlist( Netlist describes the connectivity of an electronic designs). Considering a netlist with 2 flops and 2 gates.
	
![image](https://user-images.githubusercontent.com/123365818/216350880-9917062c-aaa0-43f7-9626-616da276249c.png)
	
![image](https://user-images.githubusercontent.com/123365818/216351471-ffa5b0ce-63c9-47a8-8f8b-a06e7d20b4b4.png)

	
	lets giving the height and width of standerd cell is 1 unit. So, area of standerd cell is 1 square unit.
	And assuming the same area for the flip flops also.
	
![image](https://user-images.githubusercontent.com/123365818/216351584-009604df-56e0-4400-aea2-ef758dbbe2ce.png)


Now lets calculate the area occupied by the netlist on a silicon wafer by arranging them together. The total area occupied by netlist in silicon wafer is 4 square units.
	
![image](https://user-images.githubusercontent.com/123365818/216352266-56e4a9e6-7b6a-4b9f-b776-ccb434be3331.png)

	
A 'core' is the section of the chip where the fundamental logic of the design is placed.

A 'Die', which is consist of core, is small semiconductor material specimen on which the fundamental circuits is fabricated.

![image](https://user-images.githubusercontent.com/123365818/216352607-c39020ba-df7a-435c-9bca-66978a3a7a10.png)

If we put the 'arranged netlist' in the core, then whole core is occupied by netlist. that means utilization of core is 100%.
	
![image](https://user-images.githubusercontent.com/123365818/216353279-1925f7db-b8d3-42e7-afcf-ec5e588ecbf5.png)

**Utilization factor**
	
it is defined as, 'the area occupied by netlist' devided by the 'total area of core'.

so, in above case, the utilization factor is 1.

practically, we don't go with 100% uti;ization. practically utilization factor is about 50%-60% to add other extra cells (like buffer) in the core after netlist is placed.

**Aspect Ratio**
	
it is defined as (height)/ (width). here in above example the aspect ratio is 1.
	
Another Example,
	
![image](https://user-images.githubusercontent.com/123365818/216353574-3f9db10a-cf55-4623-b20a-d49b1474893c.png)
	
Utilization is 0.5 and the aspect ratio is also 0.5.

#### SKY_L2 - Concept of pre-placed cells

**Define locations of preplaced cells**
	
to define the preplaced cells, let's take a combinational logic i.e., mux, multiplier, clock devider, etc. and assuming that the output of the circuit is huge and assuming that the circuit has some 100k gates. so let devides in two blocks of 50k gates. 	
	This both blocks are implimented separatly. Now, extending the input and output pins from the both blocks. now, let's detached these box. we will black box the boxes and detached them. After black boxing, the upper portion is invisible from the top or invisible to the one , who is looking into the main netlist. now we make separate them out.
	
![image](https://user-images.githubusercontent.com/123365818/216355603-d60fe28f-622a-4a74-9cca-7c70f14ab8db.png)
	
This blocks are implemented in netlist once and then we can reuse it multiple time. Similarly, there are other modules or IPs also readily available.i.e.,memory, clock gating cell, comparater, mux. these all are part of the top level netlist.They recieve some signals, they perform some functions, they deliver the outputs but the functionality of the cell is implemented only once. That what we called as preplaced cells.

The arrangement of these ip's in a chip is refferd as floorplanning.These IP's have user-defined locations, and hence are placed in chip before automated placement and routing are called "preplacement cells".These cells are place din such fashion that, the placement and routing tool not touch the location of the cell.

![image](https://user-images.githubusercontent.com/123365818/216355861-f352dcdb-88b3-42d1-82e7-0d8cc0abb69d.png)

Let consider memory as a preplaced cells as a block 'a', block 'b' and block 'c'. Memory of any device is implemented once and reused multiple times. these preplaced cells are located as per the design bacckground. the location of the cells are never touched.

#### SKY_L3 - De-coupling capacitors
surround pre-placed cells with Decoupling capacitors
Let consider some circuit, which is part of the blocks which were defined above. When some gate (let consider AND gate) switched from 0 to 1 ot 1 to 0, considered amount of the switching current required because of small capacitance is available there. this capacitor should be completely charged to represent logic 1 and completly discharged to represent logic 0. the required amount of the charge is suplied from the Vdd and absorb from the Vss. during supplying the current, wire has some drop of voltage due to resistence and inductunce of the physical wire.	
	
![image](https://user-images.githubusercontent.com/123365818/216356320-bd877901-f750-4b39-b14b-e6ae1415a37f.png)

So, due to this if ideal logic 1 = 1 volt then here practically it can be less then 1 volt i.e., 0.97 volts (Vdd').
	
![image](https://user-images.githubusercontent.com/123365818/216356690-ea76b1c4-dfab-4c95-b845-f5ab28398eb4.png)

So, for any signal to be considered as Logic '0' and '1' in the NM low and NM high range. It is danger case.
	Noise Margin

![image](https://user-images.githubusercontent.com/123365818/216358817-cec293eb-ae17-4888-92c8-e3b9480772a6.png)
	
To solve this problem,, we have to put De-coupling capacitor in parallel with the circuit. 
Decoupling capacitor
	
![image](https://user-images.githubusercontent.com/123365818/216358224-9142e58c-3a0a-4d35-b440-a15fcfb7169a.png)

Every time the circuit switches, it draws current from Cd, whereas, the RL network is used to replacenish the charge into Cd.
	
![image](https://user-images.githubusercontent.com/123365818/216358911-59af7516-ee2e-4b49-935a-052cb69f458a.png)
	
#### SKY_L4 - Power Planning

	Demanding current in four macro
	Driver to Load	
![image](https://user-images.githubusercontent.com/123365818/216360287-2407a300-1576-4b2c-8b50-2c5afa267339.png)
	
	How to do power planning?

	Up to here, we have taken care of local communication. Now let consider that local circuitory as a black box and it can be repeat multiple times and power supply is also connected to all of them as shown below,

![image](https://user-images.githubusercontent.com/123365818/216361087-b591625d-fbf5-433b-a948-7b50c005748e.png)


Now 16 bit bus has to retain the same signal from driver to the load. so it should get the sufficient power from the supply. But at this bus, there is no de-coupling capacitor is available because it is not physible to put capacitor at all over the place. now, power supply is far away from the bus, that is why some drop between them will definalty occurs.
	
Let consider this 16 bit bus connected to inverter.
	
![image](https://user-images.githubusercontent.com/123365818/216361444-f44c7bbf-5275-4474-a755-5999f13ddd78.png)

 So, all capacitor are initially charged will get discharged and vice-versa due to inverter.
But the problem is occurs due to all capacitor is connected to the single ground. This will cause a bump in 'ground' tap point during discharging.
	
![image](https://user-images.githubusercontent.com/123365818/216362068-f449bd42-21ae-4fad-a080-cc8ed3fb0c87.png)
	
Same problem will occurse during the charging time also. at that time lowering of voltage occurse at 'Vdd' tap point.

![image](https://user-images.githubusercontent.com/123365818/216362301-ffe05ff1-f8f8-41f2-8523-6f77db01235d.png)

The solution of the problem is use multiple power supply. So, every block will take charge from neartest power supply and similarly dump the charge to the nearer ground. this type of power supply is called mesh.
	
![image](https://user-images.githubusercontent.com/123365818/216362834-2e3c0c63-be67-4b2d-8130-831ec4b5e4af.png)

	
	And the power planning is shown below,
![image](https://user-images.githubusercontent.com/123365818/216363015-fecad710-7058-4cba-8cd8-3eb2e1fb96ed.png)
	

#### SKY_L5 - Pin placement and logical cell placement blockage	
Pin Placement
Lets take below designs for example that needs to implemented.	

![image](https://user-images.githubusercontent.com/123365818/216365564-b38bdefd-c5a9-4c91-83e8-633fcdc4985f.png)

Both the sections has different inputs like Din1 and Din2 and operated from different clocks like clock 1 and clock 2. along with that we have preplaced cells as well. So, basically now we have 4 input ports and 3 output ports.
	

let's have one more design that needs to be implemented. this types of circuits are very much helpful to understand the timing analysis of inter clocks.

![image](https://user-images.githubusercontent.com/123365818/216366421-6aa48ebb-c80f-4f8e-a535-73447044a0c3.png)

	preplaced cell Block C
	
![image](https://user-images.githubusercontent.com/123365818/216366749-7335f6c9-7c00-4069-839f-c27f55d7bab9.png)


Now complete design becomes like given below which has 6 input ports and 5 output ports. it is called the 'Netlist'.
	
![image](https://user-images.githubusercontent.com/123365818/216369269-881effe3-b473-4489-818c-508af465910b.png)


Let's put this netlist in the core which we have designed before and let's try to fill this empty area between core and die with the pin information. The frontend team who decides the netlist input and output and the backend team who done the pin placements. So according to the pin placements, we have to locate the preplaced blocks nearer to the inputs of the preplaced blocks.

![image](https://user-images.githubusercontent.com/123365818/216368847-262e22d9-0f5a-4184-8992-e1607cc67836.png)

	
Here one thing that we noticed is that clock-in and clock-out pins are bigger in size as compared to input and output pins. reason behind this is that, input clocks are conntinuously provides the signal to the every elements of the chip and output clock should out the signal as fast as possible. So, we need least resistance path for the clocks inputs and clocks outputs. So, bigger the size, lower the resistance.

One more thing is need to take care about is that, this pin placement area is blocked for routing and cell placements. so we nned to do logical cell placement blockage. this blockage is shoown in above image in between pins.

So, floor plan is ready for Placement and Routing step.	

#### SKY_L6 - Steps to run floorplan using OpenLANE
	
	in the openlane, 
	run_floorplan
Before doing floor plann, we required some switches for the floorplanning. these we can get from the configuration from openlane.
	
	cd configuration
	ls -ltr
![image](https://user-images.githubusercontent.com/123365818/216378049-d7e68d8f-99a1-4ed9-8087-ac5d0bfed702.png)
	
	less README.md
	
![image](https://user-images.githubusercontent.com/123365818/216379083-e42efe3a-d0e3-4fc4-baaf-8bda19bd3175.png)

![image](https://user-images.githubusercontent.com/123365818/216523876-1db12fa9-d68d-489c-88eb-4b8580d38afb.png)
	
less floorplan.tcl
	
![image](https://user-images.githubusercontent.com/123365818/216380173-44fe6497-fb60-41a7-831a-87f4b620974a.png)

![image](https://user-images.githubusercontent.com/123365818/216523916-cf028634-96a3-40d3-a0ac-e02321450e82.png)

	
Here we can see that the core utilization ratio is 50% (bydefault) and aspect ratio is 1 (bydefault). similarly other information is also given. But it is not neccessory to take these values. we need to change these value as per the given requirments also.

Here FP_PDN files are set the power distribution network. These switches are set in the floorplane stage bydefault in OpenLANE.

	Go to the design file, and open config.tcl
	cd ..
	cd Designs
	cd picorv32a
	ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/216381314-4d4c3427-62de-49fb-9c88-d2b29292b428.png)
	
less config.tcl
	
![image](https://user-images.githubusercontent.com/123365818/216381635-5a0fb196-b7ea-425c-8701-ac9a01ae48d7.png)
	cd runs
	cd 01-02_08-19/ .....Dates
	less config.tcl
	
![image](https://user-images.githubusercontent.com/123365818/216383023-1a57794e-d30e-4b24-9eea-22ad68b27716.png)
	
![image](https://user-images.githubusercontent.com/123365818/216383153-2ec8354d-cce2-4a00-a7c7-59f6dac4fa25.png)


Here, (FP_IO MODE) 1, 0 means pin positioning is random but it is on equal distance.

In the OpenLANE lower priority is given to system default (floorplanning.tcl), the next priority is given to config.tcl and then priority is given to PDK varient.tcl (sky130A_sky130_fd_sc_hd_congig.tcl).

Now we see, with this settings how floorplan run.
	
In the bash window
	
	run_floorplan
	
![image](https://user-images.githubusercontent.com/123365818/216384452-7be3938f-d784-48a6-8a58-9c14d4bd4a54.png)

	
#### SKY_L7 - Review floorplan files and steps to view floorplan
      
	In the run folder, we can see the connfig.tcl file. this file contains all the configuration that are taken by the flow. if we open the config.tcl file, then we can see that which are the parameters are accepted in the current flow.
	
	cd runs
	
	cd 01-02_08-19/ 
	
	ls -ltr
	
	cd logs/floorplan
	
![image](https://user-images.githubusercontent.com/123365818/216385616-2c8ba300-45fd-49ce-b23b-cc4b4f3668bc.png)
	
	less 4-ioPlacer.log
	
![image](https://user-images.githubusercontent.com/123365818/216388273-30be6c44-ef19-481f-8341-40716621fa4a.png

![image](https://user-images.githubusercontent.com/123365818/216524003-b8779795-e03d-4c24-a862-68c790f1055b.png)

	
	cd ../../
	ls
	less config.tcl
	
![image](https://user-images.githubusercontent.com/123365818/216388933-cec297c7-b626-4fb6-94c2-d421a6af681f.png)

here we can see that, the core utilization is 35%, aspect ratio is 1 and core margin is taken as 0. while in default the core utilization is 50%. this is the issue. because this design is override the system. but it is the taken from PDK varient.tcl file. so priority vise it is true.

To watch how floorplane looks, we have to go in the results. in the result, one def( design exchange formate) file is available. if we open this file, we can see all information about die area (0 0) (660685 671405), unit distance in micron (1000). it means 1 micron means 1000 databased units. so 660685 and 671405 are databased units. and if we devide this by 1000 then we can get the dimensions of chips in micrometer.
	
	cd ..
	cd ..
	cd results/floorplan
	ls -ltr
	less picorvs2a_floorplan.def
	
![image](https://user-images.githubusercontent.com/123365818/216387768-5521d282-5862-4c0c-af2f-0d138290876d.png)

![image](https://user-images.githubusercontent.com/123365818/216524042-0468c57e-1cf5-4205-bc4c-2f2249842416.png)

	
	To see the actual layout after the flow, we have to open the **magic** file by adding the command 
**magic -T /home/nikson/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def**

![image](https://user-images.githubusercontent.com/123365818/216392389-84c4ee7e-7ec4-4b3f-98b2-43d8d2c6707f.png)

And then after pressing the enter, Magic file will open. here we can see the layout.	
	
![image](https://user-images.githubusercontent.com/123365818/216392213-63760ed7-0822-4f84-98bc-6f4a8c6334ad.png)

**magic -T /home/nu_war/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def**

![image](https://user-images.githubusercontent.com/123365818/216523655-81e69ed3-4bca-48c5-a785-188c1efd4f92.png)

**Reviewing floorplan layot with magic**

In the layout we can see that, input output pins are at equal distance.

![image](https://user-images.githubusercontent.com/123365818/216524083-bf917f22-d99d-40c8-afd3-58ebc2402cfc.png)

![image](https://user-images.githubusercontent.com/123365818/216524208-0b2e04f3-bec6-4317-b30d-1bc30a8a867d.png)

after selecting (To select object, first click on the object and then press 's' from keyboard. the object will hight lited. to zoom in the object, click on the object and then press 'z' and for zoom out press 'sft+z') one input pin, if we want to check the location or to know at on which layer it is available, we have to open tkcon window and type "what". it will shows all the details about that perticular pin.
	
![image](https://user-images.githubusercontent.com/123365818/216524429-33d04e83-9097-4433-ba94-483e53cfc854.png)
	
Along with the side rows,the Decap cells are arranged at the border of the side rows.
	
![image](https://user-images.githubusercontent.com/123365818/216524315-8ff87a42-c868-4c29-b6f8-5e5cf3a870be.png)

![image](https://user-images.githubusercontent.com/123365818/216524483-2aff8e5f-cff6-4c5a-a39a-72dc20441e5d.png)

![image](https://user-images.githubusercontent.com/123365818/216524566-d73f7c48-b489-451d-9cd6-aea638ad7323.png)

![image](https://user-images.githubusercontent.com/123365818/216524596-2972728d-1099-4df8-aed7-e1ffefd8a86a.png)

![image](https://user-images.githubusercontent.com/123365818/216524623-563a385c-c441-43dd-aaef-7cecf8cc055b.png)


so, it show that the pin is in the metal 3.similarly doing for the vertical pins, we find that this pin is at metal 2.

![image](https://user-images.githubusercontent.com/123365818/216524650-06e79cd2-78ca-489c-83c0-23e49d9067d9.png)
here we can see that first standerd cells is for buffer 1. similarly other cells are for buffer 2, AND gate etc.

### SKY130_D2_SK2 - Library Binding and Placement
	
#### SKY_L1 - Netlist binding and initial place design
	
** Bind netlist with physical cells

	Taking netlist as what we taken before,
	
![image](https://user-images.githubusercontent.com/123365818/216430251-9d5de493-558e-4f2b-9a2e-d437aa828dad.png)
	
Here, we can see that every gate or flip-flops have a shape to understand the functionality of the element.
	
![image](https://user-images.githubusercontent.com/123365818/216430900-875080a7-9dc1-43ee-a94e-52c72a298476.png)

But practically, this cells are square or rectangular boxes which has internally some logic to perform. 
	
![image](https://user-images.githubusercontent.com/123365818/216430966-efbd0fa5-2493-4d9a-af1b-8e8d94be7e72.png)

So, here we are taking all the elements from netlist and giving them a perfact height and width with perticular dimention. 
	
![image](https://user-images.githubusercontent.com/123365818/216431343-b65068e2-4c4b-4907-82d0-39b0a8c278a5.png)

These all cells together are called 'self'. And this self are stored in the lybrary. Library have all the innformation about all the blocks, like height, width , time delay, conditional innformation, etc. 

![image](https://user-images.githubusercontent.com/123365818/216431544-b775044e-ed51-4d2e-86f0-11b870637363.png)

library also have a option for the similar cells (with same functionality) like this with different height and width. According to our space available at floorplanning we can choose out of them.
	
![image](https://user-images.githubusercontent.com/123365818/216431646-d76b7f31-17e1-45f8-9769-87a7724cbfe1.png)

After giving size and shape to each and every box, next step is to take the boxes or element from library and placed in the floorplan. 
This is called placements of the cells.
	
According to the design of the netlist, we have to put physical blocks in the floorplan which we have design before.
	
![image](https://user-images.githubusercontent.com/123365818/216431958-db66c6eb-9908-4bce-86ea-9be4926fd371.png)


	
![image](https://user-images.githubusercontent.com/123365818/216432090-589e7dc8-4bdb-470d-b80e-d510189ab310.png)

Put all the blocks according to the input and output of that perticular blocks.
	
![image](https://user-images.githubusercontent.com/123365818/216432284-a90fc34c-7411-4eec-85e3-ad98e3a20fb4.png)
	
Data input 1 and output 1
	
![image](https://user-images.githubusercontent.com/123365818/216432636-a019c4ba-84fb-46f4-b1ee-cbddf98c9380.png)

Data input 2 and output 2
	
![image](https://user-images.githubusercontent.com/123365818/216432830-b16f88bd-c8d2-4b39-adb0-597acafac97a.png)
	
up to here we have done stage one and stage two placement. Now we will going for stage 3 and 4. here we have to place FF1 of stage 3 nearer to the Din3 and FF2 of stage 3 nearer to the Dout3. But Din3 and Dout3 are at somme distance from eachother. 

data out 3
	
![image](https://user-images.githubusercontent.com/123365818/216433017-ec0835cc-6be7-4e35-96b8-b44c2d28ae81.png)

Same thing is there for FF1 and FF2 of stage 4. Let's we placed these all element in such manner that all elements are closed to it's input and output pins.
Data out 4
	
![image](https://user-images.githubusercontent.com/123365818/216433197-c9876c41-2fce-4b5a-a0c2-b92eb18e5e65.png)

But, the distance of FF1 of Stage 4 and Din4 is still far them others. By optimizing the placement, we can solve this problem.
	
#### SKY_L2 - Optimize placement using estimated wire-length and capacitance

** Optimize Placement
	
As we seen that the distance from Din2 to FF1 of stage 2 is higher. 
	
![image](https://user-images.githubusercontent.com/123365818/216433966-358a7bdf-8447-4bac-98a7-6ac2cfaa85ce.png)

so if we connect the wire between them then resistance and capacitance of the wire comes in to the picture. and due to this the signal integrity can not maintain.

To maintain the integrity of the signal out from Din2 to FF1, we have to put some repeaters like buffers on between Din2 and FF1. But it will cause of loss of area.
	
![image](https://user-images.githubusercontent.com/123365818/216434242-95e66a4b-e0da-4a9c-acce-954e9e37aca0.png)

In the stage 1, there is no need of any repeater to transmit the signal. 
	
![image](https://user-images.githubusercontent.com/123365818/216434395-ed47b3b6-fc16-49c8-bf23-230fa39ff9e5.png)

But in stage 2, due to high distance, the lenth of wire is high and signal is not transmitted in perticular range. so we required repeater. sender to buffer.
	
#### SKY_L3 - Final placement optimization
	
Similar as stage 2, in Stage 3 also we required the buffer between gate 2 and FF2.
	
![image](https://user-images.githubusercontent.com/123365818/216434596-3e22a8ad-507a-4f32-b43a-1fda7691acdc.png)
	
Reproduce the same signal . Stage 4 is bit tricky as compared to other stages.
	
![image](https://user-images.githubusercontent.com/123365818/216435466-523c103d-0e83-49cd-b25f-d49e561aef31.png)
	
	Now we have to check that, what we have done is correct or not. For that we need to do Timing analysis by considering the ideal clocks and according to the data of analysis, we will understand that, the placement is correct or not.

#### SKY_L4 - Need for libraries and characterization
	
**Library charactorization and modelling**

	In whole IC design, we have to go through synthesis, floor/power planning, placement, routing , STA. 
	
Logic Synthesis
	
![image](https://user-images.githubusercontent.com/123365818/216436066-cdedd1a3-dfa5-4120-b0af-3b908f629480.png)

Floor Planning
	
![image](https://user-images.githubusercontent.com/123365818/216436209-e207f234-21cc-4708-a164-0680da2e2ef0.png)

Placement & CTS
	
![image](https://user-images.githubusercontent.com/123365818/216436389-c765f982-318f-44bb-84e0-cd5df050b468.png)

Routing
	
![image](https://user-images.githubusercontent.com/123365818/216436523-1b983bd3-7156-4028-9e4a-d3ceaea7b005.png)

STA

![image](https://user-images.githubusercontent.com/123365818/216436717-daa8b4ca-940d-49bb-a17b-c829dcf1e9f6.png)

In all this steps one thing remain common, which is "Gates or Cells". That is where the library charactorization becomes very important.
	
Gates or Cells
	
![image](https://user-images.githubusercontent.com/123365818/216436871-52cda67f-fbcb-4b73-b4d8-f225297875be.png)

Library
	
![image](https://user-images.githubusercontent.com/123365818/216437017-be45a140-23ca-4baf-b463-63f1601e1099.png)
	
#### SKY_L5 - Congestion aware placement using RePlAce
	
Right now we are not constrain about timing, but constrain about the congestion. so, we are making the congrstion is less.

cd /Desktop/work/tools/openlane_working_dir/openlane/

docker

./flow-tcl -interactive

/package require openlane 0.9

prep -design picorv32a

run_synthesis

run_floorplan

run_placement

The placement is donne in two stages. Global and detailed. In global placement, legalization is not happened but after detailed placement legalization will be done.

When we run the placement, first Global placement is happens. main objective of glibal placement is to reducing the length of wires.

In the other terminal,
cd /Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/03-02_05-15/results/placement
ls

**magic -T /home/nu_war/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def&**


Now opening the Magic file to see actual view of standerd cells placement.And the actual view in the magic file is given below,

![image](https://user-images.githubusercontent.com/123365818/216523256-c6b4811c-b439-462d-afc7-a6bc57dfda69.png)

If we zooom into this, we find the buffers, gates, flip flops in this.

![image](https://user-images.githubusercontent.com/123365818/216523378-580e33b0-5444-4f2f-81ac-cb59810445d0.png)

![image](https://user-images.githubusercontent.com/123365818/216523427-9ab7c259-3c52-41d0-a326-619d22478b9b.png)


	
### SKY130_D2_SK3 - Cell design and characterization flows

#### SKY_L1 - Inputs for cell design flow
	
**Cell design flow**

![image](https://user-images.githubusercontent.com/123365818/216437791-36271765-c301-4b0c-b033-1e6a3b440f89.png)
	
As we know that standerd cells are placed in the library.  
	
![image](https://user-images.githubusercontent.com/123365818/216437937-becde403-32da-47e0-80ef-d2a3faea2777.png)
	
And in the library many other cells are available which have same functionality but the size is different.
	
![image](https://user-images.githubusercontent.com/123365818/216438540-edd89d95-34fc-4615-bf1a-cd1e08617449.png)

As size is different the parameters like hVt, Ivt also different for each standerd cells.
	
![image](https://user-images.githubusercontent.com/123365818/216438628-f187da5d-60c8-44d2-9885-e4de2ed2a29c.png)


If we take one of the standerd cells called inverter, it has their own design flow by this it can be understandable to EDA tool.
	
![image](https://user-images.githubusercontent.com/123365818/216438846-3ff83088-bfb2-41e0-8e4b-8de4cb77948a.png)

The cell design flow is devided into three part.

	* Inputs
	
	* Design steps
	
	* Outputs
**Inputs**
inputs required for cell design is PDKs, DRC and LVS rules SPICE models, library and user defined specs.
	
![image](https://user-images.githubusercontent.com/123365818/216439061-848302b8-461c-496f-8445-16cf32725757.png)
	
![image](https://user-images.githubusercontent.com/123365818/216439266-0661e012-3044-4247-a3fd-5b0f3eca933f.png)

![image](https://user-images.githubusercontent.com/123365818/216439514-221bf5e7-626c-4581-8a7c-ec8dcd27aadf.png)

![image](https://user-images.githubusercontent.com/123365818/216439552-a7e0aeb5-f232-459c-ba33-93a1154bac0f.png)

#### SKY_L2 - Circuit design step


**Design steps**

Steps are circuit design, layout design, characterization

User defined specification

	Supply Voltage, Cell height
	
![image](https://user-images.githubusercontent.com/123365818/216440183-95d8a299-9917-4dc6-9527-86ef6e8dacea.png)

	Metal Layers
	
![image](https://user-images.githubusercontent.com/123365818/216440238-c1df2d15-fd6e-4f4e-832d-489756b30540.png)

**Outputs**

	The typical output what we get from the circuit design is CDL(circuit description language) file,GDSII,LEF,extracted spice netlist(.cir).

Layout design steps
	
implement function
	
Circuit design
	
![image](https://user-images.githubusercontent.com/123365818/216441132-c449697f-7668-41fd-9e80-dc7f05abc0fe.png)
	
Derive the NMOS and PMOS network graph
	
![image](https://user-images.githubusercontent.com/123365818/216441760-74a03fcb-a602-43dc-ae6e-97e28e3e65db.png)


Layout Design
	
Obtain the Euler's path
	
![image](https://user-images.githubusercontent.com/123365818/216441935-6f17e121-f424-485a-ad7b-f4b24d43d613.png)

Stick diagram
	
![image](https://user-images.githubusercontent.com/123365818/216442040-b1b9a2e9-8d37-440d-8a36-09891d9ba630.png)

Convert stick diagram into proper layout
	
![image](https://user-images.githubusercontent.com/123365818/216442173-26a7ba89-c8dc-42ae-87ec-04018915724f.png)

After layout design, we have to ecxtract the layout and characterize it. In characterization step, we can get the information about Timing, Noice,power.libs and function.
	
![image](https://user-images.githubusercontent.com/123365818/216442534-35a87900-3789-4feb-a13b-10c76b5da1cc.png)

#### SKY_L4 - Typical characterization flow
**Characterization Flow**

	
![image](https://user-images.githubusercontent.com/123365818/216442962-6b4a80d2-9b06-405e-8e92-2303e3ef58a7.png)
	
As of now, from the circuit design and layout design, we have final layout of buffer cell. where two buffers are connected in series with each other.

![image](https://user-images.githubusercontent.com/123365818/216443033-2cfdd0fc-59d5-4c2e-ae72-123f1f609ad9.png)

	Buffer
	
![image](https://user-images.githubusercontent.com/123365818/216443235-25f1f1f3-3a1e-4b48-a65e-a088fe00edf9.png)

	
Now steps of flow is:

1.Read the model file
	
2. read the extracted spice netlist
	
3. reconize the behavior of buffer
	
4. Read the subcircuit of the inverter
	
5. Ateched the neccessory power source
	
6. Apply the stimulus
	
7. Provide the necessory of output capacitance
	
8. Provide the necessory simulatin command
	
This all steps we have to give in "GUNA" software. and this software will give the timing, noise, power.libs and functions.
	
![image](https://user-images.githubusercontent.com/123365818/216443405-67ebe0a1-36ec-4005-812a-299bb0b76526.png)

![image](https://user-images.githubusercontent.com/123365818/216443795-025ba6b4-86e3-41cf-96b2-1471c480cd39.png)

![image](https://user-images.githubusercontent.com/123365818/216443825-b57ad95a-6485-4fdb-9bf2-a79060458eb2.png)

![image](https://user-images.githubusercontent.com/123365818/216443909-2e5da8e0-bd61-4e25-ad07-2552655b3bc9.png)

![image](https://user-images.githubusercontent.com/123365818/216443983-430daf61-7f2c-4dd3-aa3d-0914fd98c6e2.png)
	
### SKY130_D2_SK4 - General timing characterization parameters

#### SKY_L1 - Timing threshold definitions
	
**Timing threshold defination**
	
![image](https://user-images.githubusercontent.com/123365818/216444784-97b6997e-ca41-42d7-ae8e-cc84ca52f113.png)

	
Let we take the waveform from the output of the first buffer and it will be input of the second buffer and taking output of the second buffer also.
	
![image](https://user-images.githubusercontent.com/123365818/216445037-f2c139b1-b526-4fb2-b23a-b9284068557c.png)

**slew_low_rise_threshold**
	
here low means nearer to the ground, and rise tresold means we want to measer the slope of the increasing graph. typical value of slew low rise thr is around 20-30%.

![image](https://user-images.githubusercontent.com/123365818/216445219-39ad7cf2-c297-4457-9bf9-574d038e7ec5.png)

**slew_high_rise_thr**
	
same as above, high means nearer to high value.
	
![image](https://user-images.githubusercontent.com/123365818/216445303-3e27b403-7ca1-44b9-b76c-e3dd1760648c.png)

whenever we want to calculate the slew, take the point at 20% from the low and take the point at 20% from the high. according to these point, take the time data and the time difference between them will helps to calculate the slew.

**slew_low_fall_thr**
	
![image](https://user-images.githubusercontent.com/123365818/216445929-5bd71110-ccf6-45ed-a4c0-a469155682dc.png)
	
**slew_high_fall_thr**

![image](https://user-images.githubusercontent.com/123365818/216446056-041058ac-d2d4-4db9-b851-c985c6fff778.png)

NOw, taking the waveform of input stimulus which is input of the first buffer and with that taking output of the first buffer.

Similar as a slew, thresolds are for delay also available. for that same as slew, we have to take some rise and fall points from the waveforms. this tresolds are almost 50%.

**in_rise_thr**
	
![image](https://user-images.githubusercontent.com/123365818/216446187-3bde77f6-6baa-4319-a20d-509bbce3aa39.png)

**out_rise_thr**

![image](https://user-images.githubusercontent.com/123365818/216446406-24c28667-9811-4fdc-a8ce-b3a1f7b0d4f0.png)
	
**in_fall_thr**	
	
![image](https://user-images.githubusercontent.com/123365818/216446863-5292d604-9faa-4b93-a998-1af0e18cd941.png)

	
**out_fall_thr**
	
![image](https://user-images.githubusercontent.com/123365818/216446622-72cb82b9-eb3f-4175-9511-93ac9478d8ea.png)

#### SKY_L2 - Propagation delay and transition time
	
propogation delay
	
![image](https://user-images.githubusercontent.com/123365818/216447363-c60f65ec-bdc9-4133-bdbe-98c055067606.png)

let's take the same setup for understand the propogation delay. **Time delay = Time(out_thr)-time(in_thr)**
	
![image](https://user-images.githubusercontent.com/123365818/216447556-0f8d0348-8653-41dc-91c0-e525f2f8ff24.png)


Let's take waveform on which we can apply above formula.
	
![image](https://user-images.githubusercontent.com/123365818/216447744-aeb92af6-60c7-47d7-885a-9660cb25fdec.png)

	
In any case if thresold point move at the top, at that time we get negative delay because output comes before input. so reason behind the negative delay is the poor choice of the tresold points. which is not acceptable. so, choosing the thresold point is very important.
	
![image](https://user-images.githubusercontent.com/123365818/216448479-bcdd75bc-f46f-44d8-8fd1-66f1d5a7e0c5.png)
	
negative delay

Taking another example where the wire delay is very high because of high distance between two elements. so, here by choosing correct thresold point then also we get the negative delay.	

![image](https://user-images.githubusercontent.com/123365818/216448075-c3138912-b768-4ac2-a155-3936d2348b24.png)
	
**Transition time**
	
transition time = time(slew_high_rise_thr)- time(slew_low_rise_thr)

or

transition time = time(slew_high_fall_thr)- time(slew_low_fall_thr)

let's take waveform for understand the slew calculation.
	
slew rise
	
![image](https://user-images.githubusercontent.com/123365818/216449357-863c4fea-0ed0-416c-956e-61b846366a87.png)
	
slew fall
	
![image](https://user-images.githubusercontent.com/123365818/216449386-18f21cba-c912-4ba5-94f4-d58970f2164c.png)
	
Threshold Definition
	
![image](https://user-images.githubusercontent.com/123365818/216449702-e16fc22c-8561-4190-8bd4-eaeb3529b140.png)


Input slew /Output Slew	
	
![image](https://user-images.githubusercontent.com/123365818/216449200-0a497609-b085-464a-9cf8-1ffed5530169.png)

##Day 9

## Sky130 Day 3 - Design library cell using Magic Layout and ngspice characterization

### SKY130_D3_SK1 - Labs for CMOS inverter ngspice simulations


Labs for CMOS inverter ngspice simulations
SPICE deck creation for CMOS innverter
IO placer revison
Till now, we have done floor planning and run placement also. But if we want to change the floorplanning, for example, in our floor planning, pins are at equal distance and if we want to change it then we can also make it by "Set" command.

For that first we have to check the swithes in the configuration and from that we have to take the syntax "env(FP_IO_MODE) 1". and make it to the "env(FP_IO_MODE) 2". then again run the floorplanning.

Then check the changes in the pins location through magic -T.

![image](https://user-images.githubusercontent.com/123365818/216567764-88aed983-d425-4a45-92a0-24c85648d018.png)


Mode Matching 0 , 1 

![image](https://user-images.githubusercontent.com/123365818/216567070-a68c3aa2-1dee-48e5-b96c-b13b67fc1e7d.png)

set ::env(FP_IO_MODE) 2

![image](https://user-images.githubusercontent.com/123365818/216568176-6eb10fc4-dc9f-4f19-9d53-a7f515311bfc.png)

Then check the changes in the pins location through magic -T.

![image](https://user-images.githubusercontent.com/123365818/216569326-5c7ecb9a-3717-486d-a08d-bf1ca26fc5f2.png)

So, here we can see that there are no pins in the upper half side. all pins are in the lower half of the core.

#### SKY_L1 - SPICE deck creation for CMOS inverter

VTC- SPICE simulations.

Before entering into the simulation, we have to creat the spice deck. The spice deck is nothing but netlist. so, we have to creat the spice deck for CMOS.

Component connectivity

![image](https://user-images.githubusercontent.com/123365818/216570772-52d7f4a2-6e26-485d-9260-05588b21a7c7.png)

Define the components values

![image](https://user-images.githubusercontent.com/123365818/216570968-aa3db863-3712-4fc5-9c8e-615df5d53974.png)

Identyfy the nodes

![image](https://user-images.githubusercontent.com/123365818/216571171-b6f22358-58fe-4010-a073-9f93a0edd06d.png)

Name 'Nodes'

![image](https://user-images.githubusercontent.com/123365818/216571543-9d20f2c2-914e-45b2-94e6-b7e95ba3ab7f.png)

Now, let's strat the writing the SPICE deck



Here, in the syntex, It is like Name of the mosfet, drain , gate, substrait , source. so, the meaning of the syntex is that, name of the mosfet is M1, Drain is connected to OUT node, Gate is connected to IN node, Substrait is connected to Vdd and the Source is connected to Vdd node. PMOS says the type of mosfet and the Width and lenth of channel is defined. similarly, For M2, syntex were written.

#### SKY_L2 - SPICE simulation lab for CMOS inverter

SPICE simulation lab for CMOS

![image](https://user-images.githubusercontent.com/123365818/216572149-1edb56b6-9709-43cc-961c-35c9cc07f93e.png)

Tile we discribe the connectivity information about CMOS inverter only. Now we have to discribe connectivity information about other components also like source, capacitor etc. so, lets look into the other components.

First we discribe the load capacitor and then about the Vdd and Vin.

![image](https://user-images.githubusercontent.com/123365818/216572745-788981e1-9319-4581-87e9-871cd232a17d.png)

Now, we have to give simulation command. which is about swiping the Vin from 0 to 2.5 with the steps of 0.05. Because we want Vout while changing the Vin.

![image](https://user-images.githubusercontent.com/123365818/216572925-d569e8c3-4f18-45d5-bd3b-e5eeff03c4c0.png)

The final step is to discribe the Model file.Model file contains all the details about PMOS and NMOS. from this file only we get the information about PMOS and NMOS.

![image](https://user-images.githubusercontent.com/123365818/216573114-d7491ab6-ddcd-4cae-8447-6c6440aad7ea.png)



![image](https://user-images.githubusercontent.com/123365818/216573290-5e49f920-a51c-47be-b7fe-f2dacb417b03.png)

So, the total program is given below,

![image](https://user-images.githubusercontent.com/123365818/216573627-a6bc5ff3-c99c-4e20-af24-f75a343ab981.png)

![image](https://user-images.githubusercontent.com/123365818/216574093-7988ac66-ba75-49be-99ac-81b0542eac17.png)


Now, doing the simulation and get the graph like this,

![image](https://user-images.githubusercontent.com/123365818/216574551-4f87c276-2759-4bf5-9cb1-d8b48ad41dce.png)

Now, doing other simulation in which we change the PMOS width to 3 times of NMOS width. and after diong the simulation, we get the graph like this,


#### SKY_L3 - Switching Threshold Vm

CMOS is robust device
![image](https://user-images.githubusercontent.com/123365818/216575023-34c340a4-4532-45db-be96-42a9699aacde.png)

	The difference between this two graph is that in the second graph the transfer charactoristic is lies in the ecxact middle of the graph where in the first graph it is lies left from the middle of the graph.

Switching Thresold Vm
These both model of different width has their own application. By comparing this both waveform, we can see that the shape of the both waveform is same irrespective of the voltage level.

This thing tell us that when Vin is at low, output is at high and when Vin is at high, the output is at low. so the charactoristic is maintain at all kind of CMOS with different size of NMOS or PMOS. That is why CMOS logic is very widely used in the design of the gates.

Switching thresold, Vm (the point at which the device switches the level) is the one of the parameter that defined the robustness of the Inverter. Switching thresold is a point at which Vin=Vout.
CMOS inverter 's switching threshold
Vin=Vout
	
![image](https://user-images.githubusercontent.com/123365818/216575328-58408ae5-9479-47aa-ab59-86e2ca20a11a.png)

In this figure, we can see that at Vm~0.9v, Vin=Vout. This point is very critical point for the CMOS because at this point there is chance that both PMOS and NMOS are turned on. If both are turned on then there is chances of leakage current(Means current flow direcly from power to ground).

By comparing this both the graph we can understang the concept of switching thresold voltage.
![image](https://user-images.githubusercontent.com/123365818/216575773-c1ebfb4f-2c5b-4d6b-b095-dd30cb0db760.png)


![image](https://user-images.githubusercontent.com/123365818/216576051-ab931dd8-f150-479b-903b-7d35b7a772f2.png)

Idsp=-IdsN
![image](https://user-images.githubusercontent.com/123365818/216576067-83e5fab4-04ca-4d99-ba14-86ca628b4cd6.png)


Lab steps to git clone vsdstdcelldesign
To get the clone, copy the clone address from reporetery and paste in openlane terminal after the command "git clone". this will create the folder called "vsdstdcelldesign" in openlane directory.


	



	









	

	

	

   




	

























	










