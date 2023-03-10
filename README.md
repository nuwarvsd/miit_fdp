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
	
	ADC, DAC, SRAM,PLL are called as Foundry IP???s.
	
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

#### SKY_L4 - Static and dynamic simulation of CMOS inverter
	
![image](https://user-images.githubusercontent.com/123365818/216577076-fa335518-a47c-4b3c-a92a-fae788b3dd52.png)
	
Rise time ,Fall Time
	
![image](https://user-images.githubusercontent.com/123365818/216577284-452e7e84-117c-4aff-ac9e-81469e80e285.png)
	
![image](https://user-images.githubusercontent.com/123365818/216578047-d7edebc1-8756-42d2-b6c7-0223db00067a.png)

Rise Delay
![image](https://user-images.githubusercontent.com/123365818/216578500-d2156324-fb19-4a5c-80ce-cf54c5457f09.png)

Fall Delay
![image](https://user-images.githubusercontent.com/123365818/216579073-c0cb2872-7acf-4330-8f63-c70e5619ad20.png)

Rise vs Fall Delay
	
![image](https://user-images.githubusercontent.com/123365818/216579269-f85c0f64-fc4b-4966-a9f8-4f78eedd05af.png)

#### SKY_L5 - Lab steps to git clone vsdstdcelldesign

	Lab steps to git clone vsdstdcelldesign

	To get the clone, copy the clone address from reporetery and paste in openlane terminal after the command "git clone". this will create the folder called "vsdstdcelldesign" in openlane directory.
	
	git clone https://github.com/nickson-jose/vsdstdcelldesign.git
	
![image](https://user-images.githubusercontent.com/123365818/216760834-0bbab357-90d9-4d36-b4ea-370c998c6ac1.png)

	now, if we open the openlane directory, we find the vsdstdcelldesing folder in the openlane directory.
ls -ltr
	
![image](https://user-images.githubusercontent.com/123365818/216760848-9460fcb1-49e4-4899-917d-ad6caabbe62a.png)

	Now if we goes in the vsdstdcelldesign folder and open it, we get the .mag file,libs file etc.
	
![image](https://user-images.githubusercontent.com/123365818/216760912-5f2dbe4d-62ef-4148-9810-568c805285aa.png)

	now, let's open the .mag file and see that which layers are used to build the inverter. But before opening the mag file, we need tech file. so we will copy this file from this given below address,
go to the pdk folder, copy tech file
	
![image](https://user-images.githubusercontent.com/123365818/216761175-53b29c91-733b-4617-b8f6-8e951ee855f8.png)

	
cp sky130A.tech /home/nu_war/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

then go back to our vsdstdcelldesign folder,

![image](https://user-images.githubusercontent.com/123365818/216761217-a492f5b4-3a04-45cc-9018-a6e55722f716.png)

Now we can see tech file under vsdstdcelldesign folder

magic -T sky130A.tech sky130_inv.mag &

![image](https://user-images.githubusercontent.com/123365818/216761603-6fb19ec2-ffbc-4f0b-9312-aa8c905e5faa.png)
Now, we can see the layout of CMOS inverter in the magic like this, in the center
press S and P

### SKY130_D3_SK2 - Inception of Layout ???? CMOS fabrication process

####  SKY_L1 - Create Active regions
	
1. selecting a substrate
we select P-type silicon substrate with high resistivity(5~50 ohms) with moderate dopping and orientation is (100).
	dopping level 

![image](https://user-images.githubusercontent.com/123365818/216761702-6f3a4961-caf9-453c-a62a-eea38b18337a.png)

2. creating active region for transister
	Active region (pocket) identify the region of the pocket
![image](https://user-images.githubusercontent.com/123365818/216761742-3607825e-4ee0-460c-9f2b-52b76e993d6e.png)
	
	First, we create the isolation layer by depositing the Sio2 layer (~40nm) on the substrate.

very good insulator
![image](https://user-images.githubusercontent.com/123365818/216761809-40dcfdf1-a7cb-4073-8e52-e38d4070cb41.png)
	
Now, we are depisiting the Si3N4 layer (~80 nm) on the Sio2 layer.
	
![image](https://user-images.githubusercontent.com/123365818/216761853-02604ea3-8b25-43da-815a-6dfa5372a284.png)
	
Mask1: Photoresist layer (positive film or negative film of older camera)
![image](https://user-images.githubusercontent.com/123365818/216761916-d40d68c5-67db-47f0-a43b-a502e41b0768.png)

	
Now we do patterning by using depositing photoresist and using Mask 1 through UV light.
red is protecting region
![image](https://user-images.githubusercontent.com/123365818/216762021-6b3f9d70-7d4b-47d1-b7a2-1bcef9fd113c.png)

no chemical reaction under the protection region
![image](https://user-images.githubusercontent.com/123365818/216762134-f3dee340-4ee0-494d-9ca4-5fce0e4ec496.png)

Now, first we remove the mask and doing etching of Si3N4 layer on the exposed area.

![image](https://user-images.githubusercontent.com/123365818/216762188-57f26edb-19f0-443b-9fc5-59176ac66a17.png)

	Now, next step is to remove photoresist by chamical reaction, because now to Si3N4 layer itslef behaves like good protecting layer for Sio2 layer.
	
![image](https://user-images.githubusercontent.com/123365818/216762336-d58cedc0-ab02-43a8-bc30-1887e4289d4b.png)

	
	
![image](https://user-images.githubusercontent.com/123365818/216762308-40c6372d-d61e-452f-9555-ada3dd37ef5a.png)

	oxidation furnance
![image](https://user-images.githubusercontent.com/123365818/216762419-fc628ca2-6776-42fa-9e51-ee155e1911d4.png)

	now, if we do LOCOS (local oxidation of silicon) process, the exposed sio2 part will gown and bird break also form. This grown sio2 will provide the perfect isolation between two PMOS and NMOS.
	
![image](https://user-images.githubusercontent.com/123365818/216762570-2437f233-aa71-42ff-bd65-6451976648df.png)
	
Next step is to etchout the Si3N4 layer by hot phosphoric acid.
	
![image](https://user-images.githubusercontent.com/123365818/216762634-0e29e21a-a421-459e-a137-9939a64a9f3f.png)

#### SKY_L2 - Formation of N-well and P-well	
N-well and P-well formation

	Cover photoresist layer again
	
![image](https://user-images.githubusercontent.com/123365818/216762688-7d7e1e86-2913-4cf0-8371-3f7099f8fbd7.png)

we can not form P-well and N-well at a same time. we have to protect a region while forming one of the region by photoresist. And then using mask 2 and UV light, we will do patterning of photoresist to form P-well.

Cross sectional view
![image](https://user-images.githubusercontent.com/123365818/216762763-47b58ef8-1aa9-4e64-8bde-9f5f89351cc5.png)

Topview
![image](https://user-images.githubusercontent.com/123365818/216762810-3da7290e-7797-42d0-abe2-ddd8e4c6f0bb.png)

UV Expose
	
![image](https://user-images.githubusercontent.com/123365818/216762897-08e6f912-c751-44b7-8d5d-2e72450a7bf2.png)
	
Now, the area where we want to form the P-well is exposed. now we remove the mask and by applying the ion implantaton method (~200kev)to form P-well using Boron. But still it is P implant. After performing the high temparature anneling, it will become P-well.

diffuse the boron
![image](https://user-images.githubusercontent.com/123365818/216763000-0f7f05b2-674f-4cdd-ab9d-3d3d2ce58e21.png)

	We wiil do a similar process to form N-well by using mask 3 and using Phosphorus ions.
	
![image](https://user-images.githubusercontent.com/123365818/216763059-55942893-1354-4f25-b423-fd8046b652e4.png)
	
	Phosphorus implantation (Phosphorus atom is heavier than boron)
	
![image](https://user-images.githubusercontent.com/123365818/216763118-d8c0bfdb-bc99-42b9-91d8-66f52a910769.png)


	till now depth of wells are not define. so, by putting into the high temparature furnace (drive-in diffusion), we will define the depth of wells.

![image](https://user-images.githubusercontent.com/123365818/216763167-482909be-c345-42b2-ad02-b579b0853f7c.png)

	Twin well
	
![image](https://user-images.githubusercontent.com/123365818/216763205-3cfd31ef-9c34-4c58-815d-ea656b23e134.png)

#### SKY_L3 - Formation of gate terminal
	
Gate formation
	
Threshold voltage is very much depend on body effect (doping concentration)
	
![image](https://user-images.githubusercontent.com/123365818/216763398-99eeb84f-ba95-4d20-843f-dfda029302d2.png)

![image](https://user-images.githubusercontent.com/123365818/216763451-481dc0a2-7c7c-47d1-b6b0-33515ae34de1.png)

Gate terminal is the most important terminal of the PMOS and NMOS because from the gate terminal only we can control the thresold voltage. doping concentration and oxide capacitance will control the thresold voltage.
	
![image](https://user-images.githubusercontent.com/123365818/216763480-7860ab52-773a-4145-aa96-0ca787a3205c.png)

so, first we are maintain the doping concentration here. for that we use mask 4 and again doing the ion implantation of boron ion at lower energy (~60kev).
![image](https://user-images.githubusercontent.com/123365818/216763782-ae03dda8-7d90-44c6-9de0-7d03f89c1d6c.png)

same process we will repeat for N-well also by using mask 5 and Arsenic ion.
Mask 5
![image](https://user-images.githubusercontent.com/123365818/216763834-43985e20-106b-4e75-a220-1dfa53467d81.png)

Arsenic ion
![image](https://user-images.githubusercontent.com/123365818/216763871-9e7477ba-c350-4216-a33f-243032e8d303.png)
	
Next step is that we have to fix the oxide layer. but before that we have to remove the oxide layer because this layer is got dammeged because of the privious processes. so,first we remove the layer using HF solution 
	
![image](https://user-images.githubusercontent.com/123365818/216763952-f2e23856-2502-4105-b18f-0b85693feba4.png)

	and again re-grown the high quality oxide layer with same thickness.

![image](https://user-images.githubusercontent.com/123365818/216763974-2291e471-fafe-4491-bc43-6f0ecbe71105.png)

The final step is the deposition of polysilicon layer over oxide layer with more impurities for low resistance gate terminal.Then etched out this polysilicon layer by using mask 6 and photoresist.
	
![image](https://user-images.githubusercontent.com/123365818/216763993-9c0e9b31-74bd-4d56-a0ee-3f2e3376c02c.png)
	
	then more impurities
![image](https://user-images.githubusercontent.com/123365818/216764042-6343ffb8-1f36-4a0a-982b-d1c61178f306.png)

mask 6
![image](https://user-images.githubusercontent.com/123365818/216764070-0e9f9fc9-3e79-46bd-8628-4ddce73c4262.png)

Gate Mask top view
	
![image](https://user-images.githubusercontent.com/123365818/216764108-2b8e5843-a06b-4199-9727-b243cbf96cf5.png)

After etching, remove the photoresist and gate terminal looks like,
![image](https://user-images.githubusercontent.com/123365818/216764231-6b4faa16-9f8d-4492-8071-3f8379686594.png)
	
![image](https://user-images.githubusercontent.com/123365818/216764241-74be113d-6205-44c2-bc69-cc58a0bee9a9.png)

![image](https://user-images.githubusercontent.com/123365818/216764146-82b3ba0a-46e9-47c4-a3f2-ae725d0d639d.png)

#### SKY_L4 - Lightly doped drain (LDD) formation

LDD formation
source and drain doping profile
	
![image](https://user-images.githubusercontent.com/123365818/216764308-95b8425b-07ef-487b-9bbb-8b1f176ad4fd.png)

Here, we actully want P+,P-,N doping profile in the PMOS and N+,N-,P doping profile for NMOS. Reason for that is

* Hot electron effect
* short channel effect
	
Hot electron effect
![image](https://user-images.githubusercontent.com/123365818/216764430-26215d5e-2adf-4131-9bb7-edbad0ee9a3f.png)
Short Channel effect
![image](https://user-images.githubusercontent.com/123365818/216764473-beb3b715-9f72-497c-aa70-9f58a805becc.png)

For the formation of LDD, we again do ion implantation in P-well by using mask 7 and here we use phosphoros as a ion for light doping.
![image](https://user-images.githubusercontent.com/123365818/216764495-6738cec6-f247-4992-bf39-b117e5849157.png)

	Lightly Dope
	
![image](https://user-images.githubusercontent.com/123365818/216764560-1c39fc49-db32-44de-a689-5cb0fedfd74b.png)

Same process we will repeat for N-well. there we use mask 8 and BOron Ion.
	
![image](https://user-images.githubusercontent.com/123365818/216764589-1fe95568-e978-4716-9855-beee333b92a2.png)

Now, by creating the spacers, we can protect the actual structre remain constant of P-implantt and N-implant. For that we deposite a thick Sio2 or Si3N4 layer over the gate tereminal.
	
![image](https://user-images.githubusercontent.com/123365818/216764717-9b6d599c-03cc-464f-bbd1-051de1f6313e.png)

Now, we do Plasma anisotropic etching. By that side-wall spacers are formed.
	
![image](https://user-images.githubusercontent.com/123365818/216764733-633bc77a-6810-45db-b625-702325705537.png)
side well space when create source and drain (N+, remaining N-)
	
![image](https://user-images.githubusercontent.com/123365818/216764785-6f7954ab-3c2b-47d5-90dc-4d00357695d7.png)

#### SKY_L5 - Source ??nd drain formation
	
source-drain formation
Next step is deposite the very thin screen oxide layer to avoid the effect of channeling.
![image](https://user-images.githubusercontent.com/123365818/216764931-be305e31-505f-465b-8757-4a5d16e820a9.png)
masking layer 9
	
![image](https://user-images.githubusercontent.com/123365818/216764962-ed91e0d4-9ccd-4282-acf6-39b7351dee80.png)

Now to form the drain and source, again we do the ion implantation of arsenic at 75kev to create the N+ implant by using mask 9 in the P-well to form PMOS.
![image](https://user-images.githubusercontent.com/123365818/216764994-bad0acba-6b98-4278-b84d-a93b9812d6b2.png)
N+, N-
![image](https://user-images.githubusercontent.com/123365818/216765035-830dae2c-2bd4-4cc2-9c42-abbf953b1d5b.png)
Mask 10
![image](https://user-images.githubusercontent.com/123365818/216765063-87043fe9-f90a-4dae-9011-c928eb15a18f.png)

Same process we will repeat for NMOS by using the mask 10 and boron ion in the N-well at 50kev to creat P- implant.
	
![image](https://user-images.githubusercontent.com/123365818/216765092-469ed3de-8b2a-4a9f-89eb-f838e687733a.png)

Now we put this Half made CMOS into the high temparature (1000 degree)anneling. So P+ implant and N+ implant now become the source and drain.

![image](https://user-images.githubusercontent.com/123365818/216765133-9864bba0-a692-4d69-9208-cd090157029d.png)
	
#### SKY_L6 - Local interconnect formation
	
Local interconnect formation
	
7. steps tp form contacts and local interconnects
	
First step is remove the thin screen oxide layer by etching. 

![image](https://user-images.githubusercontent.com/123365818/216765191-f5ee343c-a891-478c-af65-95bff705e2ba.png)

Then deposite the titanium (Ti) using sputtering. 
	
![image](https://user-images.githubusercontent.com/123365818/216766265-b1706483-da15-45ab-8337-0839af461014.png)

Ti
	
![image](https://user-images.githubusercontent.com/123365818/216766286-0e7e9fbc-8892-4145-95ee-f12ef2380e13.png)

here Ti is used because Ti has very low resistivity.
	
![image](https://user-images.githubusercontent.com/123365818/216766312-fe71c227-7b0a-4656-be02-3fe63b7c4440.png)

Next step is to create the reaction between Ti layer and source, gate, drain of CMOS. For that wafer is heated at about 650-700 degree temparature in N2 ambient for about 60 seconds. 
	
![image](https://user-images.githubusercontent.com/123365818/216766348-594ea96f-8d09-44b9-939b-e4af2ab1c401.png)

	
and after reaction, we can see the titanium siliside over the wafer. One more reaction is heppend there between Ti and N. 


![image](https://user-images.githubusercontent.com/123365818/216766375-629668d1-3ee2-4779-bb08-3974c0feee62.png)

and it results the TIN which is used for local communication.
	
![image](https://user-images.githubusercontent.com/123365818/216766515-6e2f4198-1ad5-49a7-b55b-12d05a544299.png)
	

Now by using mask 11 and photoresist, we will etched out the TIN and make perticular contacts. 
	
![image](https://user-images.githubusercontent.com/123365818/216766566-8c9ec937-6312-4eca-a76a-8914f0dfb3d3.png)
	
TIN is etched out by using RCA cleaning.
	

![image](https://user-images.githubusercontent.com/123365818/216766622-cd4f6a09-449d-496a-8f92-1f719209d351.png)

RCA cleaning
	
![image](https://user-images.githubusercontent.com/123365818/216766698-34730c70-c9ab-495d-964d-83d0cd340761.png)

Now, local interconnects are formed after etching and removing the photoresist.

![image](https://user-images.githubusercontent.com/123365818/216766728-f0fb9aac-dcb4-41ce-bfc6-2134df625a46.png)

#### SKY_L7 - Higher level metal formation
	

8. Higher level metal formation
	
These steps are very semilar like previous steps. First thing that we are noticing is that the surface is non planner. 
It is not good to use this type of non planner serface for matel interconnects because of the problems regarding the metal disconinuty.
So, we have to plannerize the surface by depositing the thick layer of sio2 with some impurity to make less resistive layer. 
	
![image](https://user-images.githubusercontent.com/123365818/216766802-355d22ca-3d5c-4892-bc69-a3ed7448327f.png)
And then we used CMP (chemical mechanical polishing) technique to plannerise the surface.
	
![image](https://user-images.githubusercontent.com/123365818/216766862-f530a8eb-0da5-4da6-a25b-b69c5f969b13.png)

Now using mask 12 and photorsist we etched the sio2 layer to diposite the metal in it.
	
![image](https://user-images.githubusercontent.com/123365818/216766916-3a033007-3df1-4cb4-bf9c-ee64d293cf30.png)

Now remove the photoresist
	
![image](https://user-images.githubusercontent.com/123365818/216766966-6bb48083-b834-4216-925f-a19a616a0bb0.png)


and seposite the thin later of TIN (~10nm) over the wafer. 
	
![image](https://user-images.githubusercontent.com/123365818/216767062-73e983e6-0a01-43d2-87aa-037481f72004.png)

Because TiN is act as very good adession layer for sio2 and also act as a barrier between bottom layer and top layer of metal interconnects.

![image](https://user-images.githubusercontent.com/123365818/216767117-1e3f7746-370b-46ad-9df6-ff91ed1ff148.png)


Next step is to deposite the blanket tungsten (W) layer over the wafer.
	
![image](https://user-images.githubusercontent.com/123365818/216767185-84853aac-0803-4251-9655-4ada158be341.png)

	
and then do the CMP here to plannerize the surface.
	
![image](https://user-images.githubusercontent.com/123365818/216767203-4f2aad0e-36b2-49e4-9459-82d58a11bb18.png)
	
This W is act as a contact holes and this holes needs to connect to the Higher metal layer. so we will deposite the Al (aluminium) layer.

![image](https://user-images.githubusercontent.com/123365818/216767260-dd852993-3153-458c-95ca-ed6d1b139910.png)


Then by using the mask 13 and photoresist, 
	
![image](https://user-images.githubusercontent.com/123365818/216767336-1dd84c02-d388-4a5d-8a2d-53fdcc2ebc06.png)
	
we etched the W layer out to form the contact at perticular place by Plasma etching.
	
![image](https://user-images.githubusercontent.com/123365818/216767364-df1b6431-9546-4f91-bd72-b3dd1fe10352.png)

This is our first level of metal interconnets. 
	
![image](https://user-images.githubusercontent.com/123365818/216767396-0c3fb341-e595-4b9a-b0b4-ee184e3e7de9.png)
	
Metal 14 used

![image](https://user-images.githubusercontent.com/123365818/216767438-9389ae51-60d0-470c-9dfb-061cd664a28e.png)

Again TiN
![image](https://user-images.githubusercontent.com/123365818/216767488-e4e7b3c1-1509-418e-a7b6-ec9fae6cbe07.png)

W as contact	
![image](https://user-images.githubusercontent.com/123365818/216767526-f83149ae-cb05-4380-8a6d-23ddbd069cae.png)

	
now we again do the same process as above to deposite the second level of metal interconnect by using mask 14 for etched out the sio2 and using mask 15 for etched out Al leyer.
![image](https://user-images.githubusercontent.com/123365818/216767572-6db78df4-241c-440c-978c-ec2beed527ca.png)


The upper layer of Al is bit thicker as compared to lower layer of Al.Now, again deposite the layer of sio2 or si3N4 to protect the chip.
	
![image](https://user-images.githubusercontent.com/123365818/216767618-d5a088b2-8846-4d1f-9895-b7d7816d9f61.png)

	
And finally our CMOS is looks like this after the fabrication.
	
![image](https://user-images.githubusercontent.com/123365818/216767651-787ba189-1183-4199-8f83-eafcac4005eb.png)

#### SKY_L8 - Lab introduction to Sky130 basic layers layout and LEF using inverter
	
Lab introduction to Sky130 basic layer layout and LEF using inverter
In software point of view.
![image](https://user-images.githubusercontent.com/123365818/216767827-f9c1aab9-3d08-4111-aef6-08e703a83eda.png)


In sky130, every color is showing the different layer. here the firsst layer is for local interconnect shown by blue_perpel color, then second layer is metal 1 which is showm by light perple color, and the metal 2 is shown by pink color. N-well is showm by solide das line. green is N-diffusion region. and red is for polysilicon gate. similarly the brown color is for P-diffusion.
	
nwell
![image](https://user-images.githubusercontent.com/123365818/216768234-cd75501d-12cd-4000-9d43-fdcdfa28cf42.png)

In tckon window, we can see that the selected area is NMOS and similarly we can chech PMOS also. and that is how we can check that the CMOS is working or not.	
nmos	
![image](https://user-images.githubusercontent.com/123365818/216768081-deb3cef3-cf7b-4204-bbda-b320be15bba8.png)

pmos
![image](https://user-images.githubusercontent.com/123365818/216768269-2e0b3f08-6845-4a7e-9df5-ef6c0bb993f1.png)
	
n diffusion
![image](https://user-images.githubusercontent.com/123365818/216768137-9a503b42-2aac-4fde-9d08-78ab6124b088.png)
so, we can see that "Y" is attached to locali in cell def sky130_inv.


output Y
![image](https://user-images.githubusercontent.com/123365818/216768424-2472d57a-2477-4915-aa57-d87008a64faa.png)
	
we can check the source of the PMOS is connected to the ground or not. and similarly we can check it for NMOS also.
![image](https://user-images.githubusercontent.com/123365818/216768536-79044589-d264-4087-9dac-fee61691a0c9.png)

![image](https://user-images.githubusercontent.com/123365818/216768703-73af9951-ca62-4fa1-8164-2f504f166226.png)

#### SKY_L9 - Lab steps to create std cell layout and extract spice netlist	

The proposed inverter for the design is a single height standard cell, so the dimensions needs to be a multiple of the single height place site; which for sky130 node has a nomenclature of unithd with dimensions(in microns): 0.46 x 2.72 (width x height) for sky130_fd_sc_hd PDK variant. The magic tool is invoked with sky130 tech file as magic -T sky130A.tech & (the magic tech file (sky130A.tech) has also been included in this repo under /libs as reference).

Thus, the first step in magic layout tool is to create a bounding box with a width of 1.38 (3 x width(unithd)) and height of 2.72. This can be done by using command property FIXED_BBOX {0 0 138 272} in magic tkcon window.
	
![image](https://user-images.githubusercontent.com/123365818/216768877-54254def-d6c0-4b54-a388-f705ece7d332.png)
	
This is followed by defining the ground and power segments (in metal 1), the respective contacts and finally the layout of the logic part. Same procedure can be followed for any standard cell layout.
	
![image](https://user-images.githubusercontent.com/123365818/216768917-e0b82f0a-0652-42df-b9cf-c5f6f18b91bc.png)
	
To extract the file from here, we have to write the command in tckon window. and the comand is "extract all".
	
![image](https://user-images.githubusercontent.com/123365818/216769533-bbfed17d-8f52-4a62-953b-19fc50ef7193.png)

Now let's go to this location from the terminal. it is exctracted.	
![image](https://user-images.githubusercontent.com/123365818/216769603-4d65d3ad-ce62-4193-8561-47b92ce37e03.png)
	
we will use this .ext file to create the spice file to be use with our ngspice tool. 
For that we have apply the comand "ext2spice cthresh 0 rthresh 0". this will not create anything new. now again we have to type "ext2spice" comand in tckon window.
**ext2spice cthresh 0 rthresh 0**
**ext2spice**
	
![image](https://user-images.githubusercontent.com/123365818/216769990-33cf0e89-2107-46b8-a044-2f8c7f2683bd.png)

Then we are checking the location and at there spice file has been created.
	
![image](https://user-images.githubusercontent.com/123365818/216770057-e14a42bb-483d-4b1b-8975-1d1914b8594e.png)

let's see what inside the spice file by "vim sky130_inv.spice".

![image](https://user-images.githubusercontent.com/123365818/216770128-c651a1c6-c32b-434f-b2f9-230040f163da.png)

### SKY130_D3_SK3 - Sky130 Tech File Labs
	
#### SKY_L1 - Lab steps to create final SPICE deck using Sky130 tech
	
![image](https://user-images.githubusercontent.com/123365818/216770265-b0157853-e65f-4724-95dd-4c47f119ab3c.png)
	
here, we can see the all details about the connectivity of the NMOS and PMOS and about the power supply also.

X0 is NMOS and X1 is PMOS and both's connectivity is shown as GATE DRAIN SUBSTATE SOURCE.

But here the scale is 10000 um. but in Magic simulation, it is 0.01.
![image](https://user-images.githubusercontent.com/123365818/216791453-bb0077a7-6800-472b-965a-7e46e87474c9.png)
SO, we are going to change the dimension here in the terminal. so any measurement will be in this scale of 0.01u. i.e., width=37*0.01u.

Now we have to include the PMOS and NMOS lib files. it is inside the libs folder in the vsdstdcellsdesign folder.
/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/libs$ ls -ltr
![image](https://user-images.githubusercontent.com/123365818/216791678-42e0ddde-b61d-4cfa-abbf-9a17a80b378c.png)
so, now we include this file in the terminal by ".include ./libs/pshort.lib" and ".include ./libs/nshort.lib" comand.

And then set the supply voltage "VDD" to 3.3v by "VDD VPWR 0 3.3V" comand. and similarly set the value of VSS also.

Now, we need to specify the input files. by Va A VGND PULSE(0V 3.3V 0 0.1ns 2ns 4ns).

Also add the comand for the analysis like, ".tran 1n 20n", ".control" , "run",".endc",".end".
![image](https://user-images.githubusercontent.com/123365818/216792118-b2339b07-41b9-41e1-ad99-2f528436770f.png)
	
less pshort.lib
![image](https://user-images.githubusercontent.com/123365818/216792197-fe6ce7c9-6654-49a1-8472-c321b0420346.png)

after running this file we get output of ngspice like this,
	
	
### Day 10	
### SKY130_D4_SK1 - Timing modelling using delay tables	

Timing modelling using delay tables
Lab steps to convert grid info to track info
	
![image](https://user-images.githubusercontent.com/123365818/216794040-b97c16d3-9db8-4886-92b4-1fe25393309b.png)

Till now we have done synthesis, floorplaning, placement and how to extract the spice out of it, done charecterization of it. till now, for placement and routing, we doesn't required any information about input/output port,logic path, power, ground.
	


Now the concept of 'lef' file will comes into the picture. it contains all the information which we discuss earlier. so our next objective is to extract the '.lef' file out of the '.mag' file. and next step is that extracted file could be placed into the picorv32a flow.

you need to folow certain guideline while making standerd cells.the guidelines are

The input and output port must lie on the intersection of the verticle and horizontal tracks
The width of the satnderd cell should be odd multiple of the track pitch and the height should be odd multiple of track verticle pitch
Now oprning the track file from the pdk/sky130/libs.tech /openlane/sky130_fd_sc_hd/track.info. where we get the info like this,
less track.info
![image](https://user-images.githubusercontent.com/123365818/216794167-100657ba-db1b-42bd-9245-9c65edfc9ded.png)
	
so, the track is basically nothing but it is used during the routing stage.Rout will be go over the tracks. tracks are basically trases of matel layers.i.e., metal 1, matel 2, etc.

PNR is a automated. so we need to specified, where from we want routs to go. this specification is given by tracks. here we can see that each of the tracks are placed at (0.23 0.46)um horizontaly and (0.17 0.34)um vertically for li1, metal 1, metal 2 layer.

In layout, we can see that the ports are on the li1 layer. so we need to insure that the ports are on the intersection of the tracks or not. For that we have to cinvert the grid into the tracks. for that we have to first into the tracks file and the open the tckon window and type the "help grid" comand.

![image](https://user-images.githubusercontent.com/123365818/216794289-b5e15d3c-5a28-4257-a740-f3453ca7213b.png)
	
Then again write comand according to the track file.
	
![image](https://user-images.githubusercontent.com/123365818/216794415-3510aa92-2999-44f1-ad74-7a51dbb60983.png)
	
Let's see the changes in the layout.

![image](https://user-images.githubusercontent.com/123365818/216794451-7fb849aa-42de-4349-b94a-7ec3c12c14b4.png)
	
Here we can see that, as per the guideline the ports are placed at the intersection of the tracks.

![image](https://user-images.githubusercontent.com/123365818/216794537-f0ca4f43-2156-4e8f-a91f-729b5b7575fe.png)
	

now, between the boundaries, there is 3 boxes are coverd. so our second requirment also satisfied here.
	
![image](https://user-images.githubusercontent.com/123365818/216794621-a3ef661b-7b34-4c60-8df9-3234526932b8.png)

	
Lab steps to convert magic layout to std cell LEF
here already the port name and the port definationa is seted, if not seted the we have to set the definanation and the name of the port.




	
![image](https://user-images.githubusercontent.com/123365818/216794656-94341b63-abe7-4731-8756-c2d01be2f3c3.png)

As it parameters are set, we are ready to extract the 'LEF' file from the "mag" file. but before the extraction, let give the name to the cell by using tckon window.	
![image](https://user-images.githubusercontent.com/123365818/216794810-24fd5558-df9d-4a50-881c-3a62ac48f31e.png)


Now we can see this file in the vsdstdcellsdesign folder.
![image](https://user-images.githubusercontent.com/123365818/216794829-0fb4ed60-b18c-4421-89e1-49c509815d38.png)

Now, we open this file in the magic by using comand "magic -T sky130A.tech sky130_vsdinv.mag &".
	
![image](https://user-images.githubusercontent.com/123365818/216794871-02589692-85bf-48fd-b089-128ead541d3a.png)

Now to extract the lef file we have to write the comand in the tckon window "lef write". 
	
![image](https://user-images.githubusercontent.com/123365818/216794901-d6e07447-3fd6-49c4-96fc-58eb887244c0.png)

So it will create a lef file and we can check it in the vsdstdcellsdesign folder.
	
![image](https://user-images.githubusercontent.com/123365818/216794939-6881798c-8657-4192-af23-9e359163a518.png)
Now, lef file is created and now next step is plug this lef file in picorv32a. 
![image](https://user-images.githubusercontent.com/123365818/216794966-033840a5-f662-49f2-abeb-2ffd58141f24.png)
	
before that we move our files to src folder where all the design files are available at one location.
	
![image](https://user-images.githubusercontent.com/123365818/216795027-52cbbb2f-11bd-462f-9f08-4e03248751df.png)

for that we are copiying this file in the src folder by 'cp' comand.
![image](https://user-images.githubusercontent.com/123365818/216795138-9632db7a-84ff-407f-9dad-baa827f19327.png)

#### SKY_L3 - Introduction to timing libs and steps to include new cell in synthesis
The basic idea is that we have to include our custom cell into openlane flow. and the first stage in the openlane is the synthesis.
![image](https://user-images.githubusercontent.com/123365818/216795247-07980eb0-daad-434d-ac18-3488145c4af4.png)

![image](https://user-images.githubusercontent.com/123365818/216795251-8e1f5243-9eb6-4efe-8977-337ccdee0bdb.png)

here, also we required library. so we copiying the library to the src folder.

![image](https://user-images.githubusercontent.com/123365818/216795327-2fef210a-203f-48d3-8fa8-424361e5a0ec.png)
	
Before we do anything, we have to modified our congig.tcl file. so opening this file by "vim" comand and modifiying it.
![image](https://user-images.githubusercontent.com/123365818/216795352-482652a5-fdf7-44d9-a81c-4700f996cb69.png)


Now, start the new terminal and open the openlane by docker, make flow interactive and then add the package and then prep design with the privios run by the comand " prep -design pecorv32a -tag [last running time i.e.27-01_17-53] -overwrite".
	
![image](https://user-images.githubusercontent.com/123365818/216796173-16ea2c01-3be9-4902-9f38-a417a95f38d6.png)

In the design's config.tcl file add the below line to point to the lef location which is required during spice extraction.

    set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
![image](https://user-images.githubusercontent.com/123365818/216798460-fd0bc95d-8ffa-4804-96ee-3bca9a91ccdc.png)

Include the below command to include the additional lef into the flow:

    set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
  
    add_lefs -src $lefs
	
![image](https://user-images.githubusercontent.com/123365818/216798419-bcd25939-8e46-4889-9747-d785eb2c4dd3.png)


Now comes the deciding part. we have to see that the synthesis run and its maps our custom vsd inveter into this flow. so, run the synthesis.
	


Introduction to delay tables
	
Power Aware CTS
If we make enable pin at logic '1' in the AND gate, the clock will propogatee to the AND gate. similarly, if we make enable pin at logic '0' in the OR gate, here also clock propogate to the OR gate.
![image](https://user-images.githubusercontent.com/123365818/216810181-535c2961-ec43-4a1c-99b2-dcddf3c64584.png)

	
Similarly if we make enable pin at logic 'o' in the AND gate, gate will block the clock and same this will happend with the OR gate if we make enable pin at logic '1'.

The advantage of this scenario is that, during this time period of the blocking the clocks, we can save lots of power in the clock tree. now the question is that how can we use this scenario in the clock tree.

let say we have a clocktree like this given below,

![image](https://user-images.githubusercontent.com/123365818/216810212-ce38c933-4113-4c6c-a687-8bac434b987b.png)
![image](https://user-images.githubusercontent.com/123365818/216810256-a7455207-d31c-443f-a9be-22998035f0a1.png)
Here we spitted the load of the 4 FF into the 2 buffers and the load of the 2 buffers is given to the level 1 buffer. here assumptions are,

Assume c1=c2=c3=c4=25fF
Assume Cbuf1=Cbuf2=30fF
Total Cap at node 'A'=> 60fF
Total Cap at node 'B'=> 50fF
Total Cap at node 'C'=> 50fF
	
![image](https://user-images.githubusercontent.com/123365818/216810322-b3013207-e1b8-4281-8c9a-900df9dfeaea.png)

We have done some observations here,

2 levels of buffering
At every level,each node driving same load
Identical buffer at same level
so, here capacitance at the every node of the clock tree is not the same. it is varying. Now load is varying then input transition is also verying because the output load at the level 1 buffer is the input of the other buffers of level 2. so, we have variety of the delays. To capture it, we have delay tables.
	
How delay tables are prepared?
To prepare the delay table, the perticukar element is taken out of the circuit and saparetly verying the input transition and output load and according to the variation, we will charactorize the delay of the element and make the delay table frpm it.
	
![image](https://user-images.githubusercontent.com/123365818/216810366-99e5583f-cb33-46b5-b749-74a036364232.png)

![image](https://user-images.githubusercontent.com/123365818/216810383-f8610474-f9be-459b-9f02-2ae9337327d1.png)

	
#### SKY_L5 - Delay table usage Part 1
	
![image](https://user-images.githubusercontent.com/123365818/216810475-35af48fc-9cd6-404f-942e-9554aeaecdfc.png)
	
Let's looks into the sample examples and making the table for Cbuf'1' and Cbuf'2',
![image](https://user-images.githubusercontent.com/123365818/216810517-5f63edf7-ff92-4357-8cf6-e092e11ba3cb.png)

let's take practical example on the circuit. we take 40psec as input transition on the level 1 buffer and as per assumption load is around 60fF. and delay is comes between x9 and x10. lets take x9' as the delay of the buffer of level 1.
	
![image](https://user-images.githubusercontent.com/123365818/216810551-39232251-f8ed-448c-85bc-27caaddfdc3c.png)

test set
![image](https://user-images.githubusercontent.com/123365818/216810597-ac214420-94d9-406b-bb1b-2dcd2f2bb1ed.png)

#### SKY_L6 - Delay table usage Part 2 (Latency)
	
Next step is to calculate the delay of the buffers of level 2. And after that we can find the letancy at the 4 clock end points.
![image](https://user-images.githubusercontent.com/123365818/216810705-477b666a-5205-4cc2-b186-52f7d00dee12.png)


now input transition is common for both the buffers. now assuming that the transition is around the 60psec and load at both the buffers is 50fF. so it will give the delay of y15.

![image](https://user-images.githubusercontent.com/123365818/216810781-b127048b-d6cc-4b7c-b17c-3aa939407a37.png)

The total delay from input to the output is= x9' + y15.(here we are ignoring the delay of the wires). that means the skew at the any output point is zero.

If load is not same at the every nodes, the skew will not be the zero.
	
#### SKY_L7 - Lab steps to configure synthesis settings to fix slack and include vsdinv
	
After synthesis, we observed that the slack is nagative here. wns(worst negative slack)= -24.89 and tns(total negative slack)= -759.
	
![image](https://user-images.githubusercontent.com/123365818/216810976-5e905056-2b70-4c3c-b9d6-5d0aa3c39175.png)

	
let's do some modification here. for that opening the READme file from the /openlane/configuration/ less READme.md
	
![image](https://user-images.githubusercontent.com/123365818/216811268-d255afc8-2cbd-45db-9e85-3ae314c4a508.png)



Now lets try to make balance between area and the delay of the synthesis by changing the stratagy. 
	
Before: Chip area for module '\picorv32a': 147712.918400
	tns -759.46
        wns -24.89

![image](https://user-images.githubusercontent.com/123365818/216811631-703d797b-f65a-4b85-8469-575ec70518a0.png)
comand for checking the current strategy is "echo $::env(SYNTH_STRATEGY)", 0
echo $::env(SYNTH_BUFFERING), 1
echo $::env(SYNTH_SIZING),0
echo $::env(SYNTH_DRIVING_CELL), sky130_fd_sc_hd__inv_8
	
![image](https://user-images.githubusercontent.com/123365818/216812239-37e716af-d71c-49e4-9cf7-720348382404.png)

set ::env(SYNTH_SIZING) 1

![image](https://user-images.githubusercontent.com/123365818/216812048-9ff64951-5dbc-4d26-b6e0-50edd2492513.png)

And comand for changing the stategy is "set ::env(SYNTH_STRATEGY) 1". by doing this area will increase the little but but timing will improve.

Then checking the synth_bufferung and synth_sizing. if any one them is off then make it on by set the value of it by 1.

Till here we not get slack 0. to make slack 0, we ahve to write comand "set ::env(SYNTH_STRATEGY) DELAY 0"

After running synthesis we will get improved timing.
![image](https://user-images.githubusercontent.com/123365818/216812822-6771cd7d-f06c-4db0-86a9-1bbb4b14707d.png)
chip area
![image](https://user-images.githubusercontent.com/123365818/216812834-b6fe95f3-4842-4dd0-88b2-8c8799cd1fa4.png)

	
Next command for run is :

set lefs [glob $::env(DESIGN_DIR)/src/*.lef]

add_lefs -src $lefs

Then again run the synthesis.
	
![image](https://user-images.githubusercontent.com/123365818/216814977-4e1b4b1e-bac1-4188-bed2-d776e1cdd804.png)


cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/03.02_05.15/tmp

less merged.lef 
![image](https://user-images.githubusercontent.com/123365818/216813244-28a46bda-33fc-4ffb-8496-5aa0ad6069c5.png)
	
Clock tree synthesis TritonCTS and signal integrity
Clock tree routing and buffering uisng H-Tree algorithm
what is clock tree synthesis?
As shown in below, figure, let's connect clk1 to FF1 & FF2 of stage 1 and FF1 of stage 3 and FF2 of stage 4 with out any rules.



Timing analysis with ideal clocks using openSTA
Now next step is run floorplan, place IO, do global placement or detail placement and genrate pdn file the run the cts by following comands.
![image](https://user-images.githubusercontent.com/123365818/216879513-6f835192-10a4-4ddc-bf99-43fdbff3d287.png)

run_placement
![image](https://user-images.githubusercontent.com/123365818/216814031-297698c6-2c36-4168-a6db-7b0211d51048.png)
	
Then check the file which is created. Go to the placements folder under results and then invoke the magic tool and load the def file. The command is:magic -T /home/nu_war/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def&
	
![image](https://user-images.githubusercontent.com/123365818/216814699-05f9452d-7dca-42dd-8367-4310c22661bf.png)

![image](https://user-images.githubusercontent.com/123365818/216814918-f1e658ee-f20d-4ff2-b850-6e27c298dbd0.png)
![image](https://user-images.githubusercontent.com/123365818/216814926-07d202f9-6f9d-4d9a-ba44-ddcdf598e8c2.png)

	
### SKY130_D4_SK2 - Timing analysis with ideal clocks using openSTA
#### SKY_L1 - Setup timing analysis and introduction to flip-flop setup time
	
iming analysis (with ideal clock)
we start with taking the ideal clock and we will do timing analysis for the ideal clock first.

Let's start the setup analysis with the ideal clock(single clock). specifications of the clock is

clock frequency =1 GHz
clock period =1 nsec

![image](https://user-images.githubusercontent.com/123365818/216823954-dd513d4e-37ab-4a7f-8b95-ab867ccdc4ae.png)
Let's take lainch Flop and Capture flop with clock. here clock tree is not built yet. so it is ideal scenario. here we have to do analysis between '0' and 'T'. with that assume that the delay of logic is '??'.
	
![image](https://user-images.githubusercontent.com/123365818/216824004-8f826c76-1cb8-49d2-b276-204e19adff5e.png)
Setup timing analysis says that ??<T. this condition should be neccessory for the the comninational logic work.

Now let's introduce the practical scenario here. Opening the capture flop and it has two mux inside it.
![image](https://user-images.githubusercontent.com/123365818/216824077-473012b9-fe46-44f7-bd32-d2c93edf818e.png)
The way flop work, it will shown by the timing graph like this,
o, here mux 1 and mux 2 both have their own delay. these delay will restrict the combination delay to the requirment.

Hence finite time 's' required before clk edge for 'D' to reach Qm.

So, we can write that the internal delay of the MUX1 = set up time(S).
![image](https://user-images.githubusercontent.com/123365818/216824155-f7020e92-908f-4875-babf-ba6b1f4603d2.png)

So, now ??<T becomes ??<(T-S).
	
#### SKY_L2 - Introduction to clock jitter and uncertainty
	
Let's bring one more practical scenario here. clock is taking from the some clock source or PLL. 
	
![image](https://user-images.githubusercontent.com/123365818/216824241-dab97153-5c5f-43f1-b4f9-8c109b6b2cf2.png)

So, because of some delay from the practical source of clock or PLL, clock pulse will not comes exacly at t=0 or at t=T. that in built variation of the clock is called jitter.
				     
![image](https://user-images.githubusercontent.com/123365818/216824338-917e15f9-5a68-47a6-b69e-a796ce275fce.png)

lets consider this uncertantity time(US) in consideration. So, now equation becomes like, ??<(T-S-US). Now assuming that 'S'=0.01ns and 'US'=0.09ns. by taking that, lets identify the timing path for our existing scenario. in our circuit stage 1 and stage 3 logic path has single clock.
![image](https://user-images.githubusercontent.com/123365818/216824397-27b05bea-b0f8-4433-ba53-eb6de03e25ad.png)
setup time
![image](https://user-images.githubusercontent.com/123365818/216824437-45a9f33a-9fba-4e61-8bcf-ce004ab67d58.png)

NOw, what we have to do is identify the combinational path delay for the given both logics.
![image](https://user-images.githubusercontent.com/123365818/216824580-40580308-94b4-4a4e-8aa1-ef170259ddc6.png)
![image](https://user-images.githubusercontent.com/123365818/216824594-4f80e5aa-44a2-474f-a14c-83ddc93b7847.png)
![image](https://user-images.githubusercontent.com/123365818/216824617-3691df3f-6a56-4929-b503-c10abf7b5ccd.png)
combinational logic delay should less than 0.9 ns
![image](https://user-images.githubusercontent.com/123365818/216824630-caa249d6-c00e-4253-8583-0af928588058.png)

#### SKY_L3 - Lab steps to configure OpenSTA for post-synth timing analysis
vim pre_sta.conf
![image](https://user-images.githubusercontent.com/123365818/216828893-4b9c129b-ec63-40e6-abef-3742daa253ba.png)

Create file with the following commands under openlane folder
set_cmd_units -time ns -capacitance pF -current mA -voltage V -resistance kOhm -distance um
read_liberty -min </location/to/min_lib>
read_liberty -max </location/to/max_lib>
read_verilog </location/to/verilog_file>
link_design <Top-Module-name>
read_sdc </location/to/sdc_file>
report_checks -path_delay min_max -fields {slew trans net cap input_pin}
report_tns
report_wns

when we do CTS, CTS is a stage where, we add clock buffers along with clockpath and build the clock tree. so, actually we are changing the netlist. Along the running CTS, actually the netlist file also created. so, after running the CTS, we will see the new Verilog file here.

Now, we see what is in the my_base.sdc file.	
vim my_base.sdc
![image](https://user-images.githubusercontent.com/123365818/216829067-a5394fef-f5dc-4b99-af2a-ea0b92fb562c.png)

Create file with the following commands under src folder
set ::env(CLOCK_PORT) clk
set ::env(CLOCK_PERIOD) 12.000
set ::env(SYNTH_DRIVING_CELL) sky130_fd_sc_hd__inv_8
set ::env(SYNTH_DRIVING_CELL_PIN) Y
set ::env(SYNTH_CAP_LOAD) 17.65
create_clock [get_ports $::env(CLOCK_PORT)]  -name $::env(CLOCK_PORT)  -period $::env(CLOCK_PERIOD)
set IO_PCT  0.2
set input_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
set output_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
puts "\[INFO\]: Setting output delay to: $output_delay_value"
puts "\[INFO\]: Setting input delay to: $input_delay_value"


set clk_indx [lsearch [all_inputs] [get_port $::env(CLOCK_PORT)]]
#set rst_indx [lsearch [all_inputs] [get_port resetn]]
set all_inputs_wo_clk [lreplace [all_inputs] $clk_indx $clk_indx]
#set all_inputs_wo_clk_rst [lreplace $all_inputs_wo_clk $rst_indx $rst_indx]
set all_inputs_wo_clk_rst $all_inputs_wo_clk


# correct resetn
set_input_delay $input_delay_value  -clock [get_clocks $::env(CLOCK_PORT)] $all_inputs_wo_clk_rst
#set_input_delay 0.0 -clock [get_clocks $::env(CLOCK_PORT)] {resetn}
set_output_delay $output_delay_value  -clock [get_clocks $::env(CLOCK_PORT)] [all_outputs]

# TODO set this as parameter
set_driving_cell -lib_cell $::env(SYNTH_DRIVING_CELL) -pin $::env(SYNTH_DRIVING_CELL_PIN) [all_inputs]
set cap_load [expr $::env(SYNTH_CAP_LOAD) / 1000.0]
puts "\[INFO\]: Setting load to: $cap_load"
set_load  $cap_load [all_outputs]
by running the command
sta pre_sta.conf
![image](https://user-images.githubusercontent.com/123365818/216828971-c1b3ba56-5fcb-42f4-bca3-fdcf1bcdc23f.png)

![image](https://user-images.githubusercontent.com/123365818/216828315-4da7bd6b-e390-4141-9f71-d5f436585a1f.png)

![image](https://user-images.githubusercontent.com/123365818/216828716-bc686636-6542-423a-93e9-c9db65b232f2.png)

![image](https://user-images.githubusercontent.com/123365818/216828785-2cb74498-9446-471c-9b11-ca8318cf01ef.png)

### SKY130_D4_SK3 - Clock tree synthesis TritonCTS and signal integrity
#### SKY_L1 - Clock tree routing and buffering using H-Tree algorithm
what is clock tree synthesis?
As shown in below, figure, let's connect clk1 to FF1 & FF2 of stage 1 and FF1 of stage 3 and FF2 of stage 4 with out any rules.
![image](https://user-images.githubusercontent.com/123365818/216829217-ee9cfdb0-db08-4be5-92d2-34651042877e.png)

Now, let's see the problem with this clock Rout. let us say time required to reach the FF1 and FF2 of stage 1 are t1 and t2 for clock1. so, we can say that t2-t1= skew (ideally skew=o).
![image](https://user-images.githubusercontent.com/123365818/216829259-fc07d82a-5282-4f75-8dd6-32343286d54b.png)

To make, skew to be 0, this rout definatly not help. so, we can say that what we built the tree is "BAD TREE". so, the concept of H-tree comes out. with the Mid point strategy, H-tree form.

![image](https://user-images.githubusercontent.com/123365818/216829450-080dc321-51c6-465a-a7f6-16d579fa52c5.png)


Next thing is clock tree synthesis (buffering).as we see in the clockk tree and we observed that clock has to travel through all wires and it will charge all capacitor which are comming in the path of this wire.

![image](https://user-images.githubusercontent.com/123365818/216829485-84220b0b-52de-497e-a1bf-2fa2136b7210.png)

The problem occurse due to the charging the capacitor is signal inntigrity problem because of transition of signals. solution of this problem is add the repeaters here. the repaters are the similar as what we use in the data path but the main difference is, here repeater has equal rise and fall time.
![image](https://user-images.githubusercontent.com/123365818/216829556-7c329796-93c9-4ece-be4c-927874e7fe16.png)

#### SKY_L2 - Crosstalk and clock net shielding

Sheild the clock net
![image](https://user-images.githubusercontent.com/123365818/216829725-2efc19e6-aa57-4667-a543-7a78e28dff04.png)

We build the clock tree in a manners that the skew becomes zero between launch Flop and capture flop. but if accedently any crosstalk heppense then everything what we had design is detoriated.

![image](https://user-images.githubusercontent.com/123365818/216829758-332dd8ee-227c-4b2c-9b4f-29ba2b55a9c0.png)

Let take the first clock net and protect it by shielding. here we protect the clock network from outside world. if the protection is not there then problems like glitch and delta delay is heppens.

![image](https://user-images.githubusercontent.com/123365818/216829804-c8e37a2f-1137-4422-805f-91ec7a6d7e3a.png)


The glitch is heppence because of Coupling capacitance between the wires.


The shielding is the technique, by which we can protect the net from these problems. In a shielding, we put wire between the other teo wire where coupling capacitance is generate. This extra wire is grounded or connected to VDD.

![image](https://user-images.githubusercontent.com/123365818/216829848-6f96a1d7-c174-44a1-bf20-abe4df25ab49.png)

Now, let's see about the delta delay.

### SKY130_D4_SK4 - Timing analysis with real clocks using openSTA

#### SKY_L1 - Setup timing analysis using real clocks

With real clock, circuit looks littel bit different then ideal clock. Here the bufferes and wires are added to the circuts.

Here, due to buffer, clock signals are not reaching the flop at t=0. it will reach at t=0+(delay of buffer 1 and 2).Now equation change to (??+1+2)<(T+1+3+4).
![image](https://user-images.githubusercontent.com/123365818/216830179-ee0a3804-7716-42f0-86a0-2f9d2416afda.png)
Let's called "1+2"=???1 and "1+3+4"=???2 and (???1-???2)=skew
![image](https://user-images.githubusercontent.com/123365818/216830280-304d0a8d-f16d-4710-94fb-0c6007114e9e.png)
![image](https://user-images.githubusercontent.com/123365818/216830311-89535d5e-ef53-4448-89c6-42456eccd5fe.png)

And here also, we have to consider the propogation skew (s) and uncertainty delay (US). so final equaltion becomes like, (??+???1)<(T+???2-S-US).
![image](https://user-images.githubusercontent.com/123365818/216830362-59d98bec-e79f-423d-af89-7af839e0d67d.png)

we can also say that (??+???1)= data arrival time and (T+???2-S-US)=data required time.
If (Data required time)- (Data arrival time) = +ve then it is fine. If it is -Ve then it is called 'slack'.
![image](https://user-images.githubusercontent.com/123365818/216830429-b936dff7-c9ea-40df-a827-ab01a8952c81.png)

Hold timing analysis
It is littel bit different then setup timing analysis. here we are sending the first pulse to the both launch FLop and capture flop.
![image](https://user-images.githubusercontent.com/123365818/216830490-0e392be0-fd88-45ad-b2a0-5e44af854a98.png)

Hold condition state that, Hold time (H)< combinational delay (??). So, (??>H).
![image](https://user-images.githubusercontent.com/123365818/216830617-5b8b4b0a-a018-4126-9f83-be0fd043ece4.png)

Hence, finite time 'H' required for 'Qm' to reach Q i.e., internal delay of mux2= hold time.
![image](https://user-images.githubusercontent.com/123365818/216830654-6a2534cb-111a-4fd6-b5e1-74681a4b062f.png)

#### SKY_L2 - Hold timing analysis using real clocks

Now here also adding the Unsetainty delay(UH) value due to jitter. so, equation becomes like, (??+???1)>(H+???2+UH).
![image](https://user-images.githubusercontent.com/123365818/216830759-576915c4-b19e-48f1-9dfa-d5a8f28c9007.png)

we can also say that (??+???1)= data arrival time and (H+???2+UH)=data required time.
![image](https://user-images.githubusercontent.com/123365818/216830812-1bedc32a-00ea-47c2-af86-6fdb2031e162.png)

If (Data arrival time) -(Data required time)= +ve then it is fine. If it is -Ve then it is called 'slack'.
![image](https://user-images.githubusercontent.com/123365818/216830856-fc27c95e-b6d6-44e2-85af-0f87aeb837d2.png)

Now, applying all these things in out network.

Now, if we add the real time clock, the equation will be change. now equation becomes (??+???1)>(H+???2).
![image](https://user-images.githubusercontent.com/123365818/216830883-10266c2c-4021-4790-b456-04ea8db03a7b.png)

delta 1
![image](https://user-images.githubusercontent.com/123365818/216830955-5cea66c6-9f0b-4f12-878a-b7b1d0a56361.png)
delta 2
![image](https://user-images.githubusercontent.com/123365818/216830968-5d99a069-be2b-49ab-8d16-31920acd7f26.png)
skew
![image](https://user-images.githubusercontent.com/123365818/216831065-b8f418b2-21ee-4a76-aeb6-1ab07bf3c2ae.png)

setup time
![image](https://user-images.githubusercontent.com/123365818/216831075-158c888b-9317-4235-b32b-6c443df54bd5.png)
Hold time
![image](https://user-images.githubusercontent.com/123365818/216831091-41551be3-89d9-4dcf-82f9-2e05d6a23822.png)

#### SKY_L3 - Lab steps to analyze timing with real clocks using OpenSTA

let's open the OPENROAD tool in the flow by "openroad" command.

our objective is to do analysis of the clock tree.

we are analysin this in the OpenROAD because OpenSTA is already built in the OpenROAD. In OpenROAD the timing analysis is done in a different way. first we have to create "db" and "db" is created in a "lef" and "def" file.

Now let's create the DB. To create the DB, first we have to read the lrf file by comand "% read_lef /openLANE_flow/designs/picorv32a/runs/29-01_22-23/tmp/merged.lef".

Then we read the "def" file by command: "read_def /openLANE_flow/designs/picorv32a/runs/29-01_22-23/results/cts/picorv32a.cts.def".

NOw to create the DB write the command "write_db pico_cts.db"

NOW read this db file by command "read_db pico_cts.db"

then read the verilog file by applying the command "read_verilog /openLANE_flow/designs/picorv32a/runs/29-01_22-23/results/synthesis/picorv32a.synthesis_cts.v"

then read the library (max) by this command:"read_liberty -max $::env(LIB_FASTEST)".

similarly read the library (min) by this command: "read_liberty -min $::env(LIB_SLOWEST)".

Now read the sdc file by this command: "read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc"

now set the clocks by this command:"set_propagated_clock [all_clocks]"

there reports the checks by this command: "report_checks -path_delay min_max -format full_clock_expanded -digits 4".

so after running this we can see that the slack is positive for hold and setup both. and also we can notice the data required time and data arroval time also.

So, the Hold slack = 1.6982nsec because here we can see that (arrivel time) >(required time).


#### SKY_L4 - Lab steps to execute OpenSTA with right timing libraries and CTS assignment
TritonCTS is right now built according to optimize fully according to one corner and we had bulid the clock tree for typical corner. and library also min and max. so we made tree according to typical corner but we analize it according to one corner. so, analysis become incorrect.
![image](https://user-images.githubusercontent.com/123365818/216879016-82b3830b-d48f-4091-a743-d5255401babc.png)

so, first we exits from the openroad by using "exit" command and we have to include the typical library for typical analysis. for that we have to open the "openroad" again and add this typical library. now we don't need to add lef and def file here. now commands for the adding a file is:

read_db pico_cts.db

read_verilog /openLANE_flow/designs/picorv32a/runs/29-01_22-23/results/synthesis/picorv32a.synthesis_cts.v

read_liberty $::env(LIB_SYNTH_COMPLETE)

link_design picorv32a

read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

set_propagated_clock [all_clocks]

report_checks -path_delay min_max -format full_clock_expanded -digits 4

slack for typical coirner= 0.2429nsec


Now checking the branch buffer cells by command :"echo $::env(CTS_CLK_BUFFER_LIST)". and these are the buffer cells are listed there "sky130_fd_sc_hd__clkbuf_1 sky130_fd_sc_hd__clkbuf_2 sky130_fd_sc_hd__clkbuf_4 sky130_fd_sc_hd__clkbuf_8".

when openlane are making the CTS, at that time this buffers are place in the clock path to meet the skew value. and we always want skew value is maximum to the 10% of clock period.
## Day 11
## Sky130 Day 5 - Final steps for RTL2GDS using tritonRoute and openSTA
### SKY130_D5_SK1 - Routing and design rule check (DRC)
#### SKY_L1 - Introduction to Maze Routing ???? Lee????s algorithm

Next stage in the physical design is the routing and DRC stage.

Routing. by the name it is says that rout means make physical contact between Din1 and FF1 od stage 1. but algorithm wise,it understand that Din1 is the source and FF1 input pin is the target. so, algorthm has to find the best possible solution to connect the Din1 and FF1.

For that we use Maze Routing and Lee's algorithm. let's try to connect Block 1 and block 2 of stage 3. there are varies mathod to connect these blocks but we need best solution for the rout or connection. To understand that, we remove all other things.

![image](https://user-images.githubusercontent.com/123365818/216831395-34040819-42fe-46be-ad2d-036449eba843.png)

Algorithm create the routing gird at backend. here algirithm create two points. one is source and other is target. 

![image](https://user-images.githubusercontent.com/123365818/216831496-7c799318-43e9-4fe3-b602-56f351033dd5.png)
Now by using this routing grid, algorithm find the best way of routing. algorithm marks the adgecent grids of source. similarly again it will find the adgecent grids of the previos grids.similarly this process will runs continuosly.

![image](https://user-images.githubusercontent.com/123365818/216831598-c9f09ada-9be2-4d2c-a3d6-852c1d0bdfb3.png)

![image](https://user-images.githubusercontent.com/123365818/216831682-ede32aeb-d502-4647-b0b5-5f4c67ab8239.png)

#### lSKY_L2 - Lee????s Algorithm conclusion

The extanding process of adgecent grids are contionuous till it reaches to the target.
![image](https://user-images.githubusercontent.com/123365818/216831814-f85bec3d-a084-465c-9e66-b90ba8344ef1.png)

there is many ways to reach the target. 
![image](https://user-images.githubusercontent.com/123365818/216831848-ca57aeb4-d83e-4fc5-b540-6d9dad1c910b.png)

![image](https://user-images.githubusercontent.com/123365818/216831885-56bdaa1d-7a8d-4684-b593-963caa8fc2ea.png)
Single bend is more prefered
![image](https://user-images.githubusercontent.com/123365818/216831937-5e3d74b1-08eb-42df-a90d-26578c870eba.png)

but the best possible way is 'L' shaped way.

![image](https://user-images.githubusercontent.com/123365818/216831944-646f4f97-7c0f-4e1f-9952-f5b7e35f7bba.png)

lets take another example for this routing.
![image](https://user-images.githubusercontent.com/123365818/216832088-8a94cf17-4313-4237-8199-0ea61b4f8914.png)
![image](https://user-images.githubusercontent.com/123365818/216832119-7f88f82e-bb43-4654-9b03-624d3511be1c.png)

![image](https://user-images.githubusercontent.com/123365818/216832024-8c53d7f2-1dcd-4611-b07f-9ca457fe411b.png)

![image](https://user-images.githubusercontent.com/123365818/216832139-f240bce7-98ac-4d26-9a91-42bab8e5d38e.png)
Now, we reouted all the blocks and the circuits looks like this,
![image](https://user-images.githubusercontent.com/123365818/216832161-c41f5692-0d51-4236-b2bc-7d5465d918d6.png)

#### SKY_L3 - Design Rule Check
While doing the routing, we need to follow certain rules for that. this is called DRC cleaning.
![image](https://user-images.githubusercontent.com/123365818/216832622-7d0c4817-a5fa-4d0c-ba03-a1077a9b92d7.png)

Lets take two parallel wires from the circuit for example,
Rule 1
Width of the wire should be minimum that derived from the optical wavelenth of lithography technique applied.

![image](https://user-images.githubusercontent.com/123365818/216832236-8e869eba-ea45-402f-bf1a-e88fcd5bb0dc.png)

Rule 2
The minimum pitch between two wire shold be this much.
![image](https://user-images.githubusercontent.com/123365818/216832489-ba057333-1287-4618-aa97-bdc75ae27316.png)

Rule 3
The wire spacing between two wires should be this much.
![image](https://user-images.githubusercontent.com/123365818/216832535-73ff844e-2b7b-4c99-b969-bd8bf9507c36.png)

Let's take other part for understand the rules. basic problem in this types of wire is the signal short.
![image](https://user-images.githubusercontent.com/123365818/216832340-cae529f6-495a-4e10-b8ee-914b9cef59b1.png)

Solution of this signal short problem is take one of the wire and put it on the other metal layer. usually upper metal is wider than the lower metal.

After this solution, we add two new DRC rules should be check.

Rule 1
via width should be some minimum value.
![image](https://user-images.githubusercontent.com/123365818/216832705-638b7863-43ca-4121-b717-38b9902f7a89.png)

Rule 2
Via spacing should be minimum this.
![image](https://user-images.githubusercontent.com/123365818/216832747-d62d6312-6001-4f0e-8930-56f32d26e85e.png)
Next step is paracitic Extraction. so, the wire will get some resistance and capacitance value.
![image](https://user-images.githubusercontent.com/123365818/216832809-af12ccaf-5e7c-4613-b50e-3c04798902ab.png)

#### SKY_L1 - Lab steps to build power distribution network
IS in case, our terminal is delected by some cause, the if we want the previos terminal once again then this steps should be followed:

docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a -tag [run file name i.e., 20-01_22-23]
03.02_05.15
Now to check the which was the last stage we perorm, the command is:"echo $::env(CURRENT_DEF)".
![image](https://user-images.githubusercontent.com/123365818/216877711-71e65645-4545-4835-b5bc-469925e9ff13.png)


So, till now we have done CTS and now we are going to do the routing. but before routing we have to generate the PDN(power distribution network)file. for that command is: "gen_pdn".

![image](https://user-images.githubusercontent.com/123365818/216877337-d03c020a-e8e6-418a-a1a8-e7ce27174882.png)

Here we can see the total number of nodes on the net VGND and it is also says that Grid matrix is created successfully. here total connection between all PDN nodes establish in the net VGND.

Now, till here we have picorv32a chip, and it needs the power. so it will get power from VDD and GND pads. From the pads, power goes to the tracks and from the tracks, the cells get power.
![image](https://user-images.githubusercontent.com/123365818/216878084-a3578a5f-cb92-4696-b6b9-783a24a69ead.png)

#### SKY_L2 - Lab steps from power straps to std cell power

To understan this, take an example here,

![image](https://user-images.githubusercontent.com/123365818/216878224-3d979abe-e6b8-44d2-a6de-6bc7485541b8.png)

In this figure, the green box is available is let say picorv32a chip. And the yellow, red and blue boxes which are the shown on the outside of the frame are the I/O pins and the power and ground pads. in this pads, the corner ones are called corner pads.

Red pads are the power pads and Blue pads are ground pads.

Power is transfered to the rings from the pads through Via which is shown by black dots on the cross section points of the ring and pads.

Now we need to insure that the power is transfered from the ring to the chip. for that we have vertiocal and horizontal tracks which are also shown by the red and blue color.

NOw, we need to supply power to the standerd cell (Which are shown by rectangular white boxes) from these tracks. this is done by horizontal small connections shown in the figure.

So, in a lab we have done this all the things till power distribution.

![image](https://user-images.githubusercontent.com/123365818/216878434-d2ffdc22-722c-4d90-974f-475a5860eadc.png)


Now next and the final step is routing.

#### SKY_L3 - Basics of global and detail routing and configure TritonRoute

power generation
![image](https://user-images.githubusercontent.com/123365818/216878634-01be38c9-2f2a-4ed7-bd8b-f2cdba0473a9.png)

Now current def.file is change to pdn.def from the cts.def. pdn.def file is now in the "runs/29-01_22-23/tem/floorplan/pdn.def".

In the routing process we are focusing on the routing strategy. there are 5 routing strategies are there. 0,1,2,3 and 14. routing is done in the TritonRoute engine. we have to specifies the strategy for the routing. for example if we ser the strategy to 14 then "TritonRoute14" strategy is used.

![image](https://user-images.githubusercontent.com/123365818/216878951-fa9aa365-c702-4b11-9598-fa5609641412.png)


If we set the TritonRouting strategy to "0" then it want converge to a 0 TRC routing. but because of this we will improve in the memory requirement and run time. if we use TritonRoute14 then the run time will be approximate 1 hours. but in the TritonRoute0 it will be around the 30 minutes. here we use the "TritonRoute0". so in our flow first we check the routing strategy by the command "echo $::env(ROUTING_STRATEGY)". if it is "0" then it is fine, otherwise we have to change the strategy to "0".

Now the last thing remains is routing. for that command is :"run_routing".
![image](https://user-images.githubusercontent.com/123365818/216880410-91c7cabd-9600-4a30-aff1-49e39cbc1602.png)

![image](https://user-images.githubusercontent.com/123365818/216880441-8df2227d-19e7-4120-8e7c-2f82a69d17dc.png)

![image](https://user-images.githubusercontent.com/123365818/216880469-ff73d7b5-20ba-4652-afb5-ff093b4e8f2a.png)

![image](https://user-images.githubusercontent.com/123365818/216880511-3f61ebca-d757-4089-882f-afd8e6882fd4.png)


The routing process is very complex.So, total routing is devided into two part.

Fast route (Global route)
Detailed route
![image](https://user-images.githubusercontent.com/123365818/216881313-3b2f6a51-cd2b-45e1-a9f6-2000cde4062b.png)
In the Global route, the routing region is devided into the rectangular grids cells as shown in the figure. and it is represented as 3D routing graph. Global route is done by FAST route engine.The detailed route is done by TritonRoute engine.

As shown in the figure, A,B,C,D are four pins which we want to connects through routing. and this whole image of A,B,C,D show the nets.

Now, the routing is successfully done.
![image](https://user-images.githubusercontent.com/123365818/216882489-3b67cbf4-ae9f-4d4d-a728-9d2b0fec7302.png)

#### SKY_L1 - TritonRoute feature 1 - Honors pre-processed route guides

![image](https://user-images.githubusercontent.com/123365818/216882387-43005534-74e2-47ed-a16a-7d7e77b529b1.png)

It performs initial detail route.
It attempts as much as possible to route within route guides.
requierment of processed guides are 1)should have within unit lenth 2)should be in the preferred direction.

![image](https://user-images.githubusercontent.com/123365818/216882337-5ebd03d1-59c3-4b29-80e4-e1ac866e6857.png)

Assumes route guides for each net satisfy inter-guide connectivity.
If two guides are connected then 1) they are on the same metal layer with touching edges. 2) they are on the neighbouring metal layers with a nonzero vertically overlapped area.
Assumes route guides for each net satisfy inter-guide connectivity.

#### SKY_L2 - TritonRoute Feature2 & 3 - Inter-guide connectivity and intra- & inter-layer routing

Each unconnected terminal. i.e, poin of a standerd-cell instance should have its pin shape overlapped by a route guide.
![image](https://user-images.githubusercontent.com/123365818/216901980-a0e784e4-e536-4d15-bcd8-5cd8cd67f4e5.png)

Here we can see that black dots are pins of the cells and it is overlapped by route guide. if you have pins on the intersection of the vertical and horizontal tracks that will ensure that it will be overlapped by route guides.

Intra-layer parallel and inter-layer sequential panel routing
Intra layer means within the layers and inter layear means between the layers.


![image](https://user-images.githubusercontent.com/123365818/216901787-f4847984-d2c2-42bb-81ba-f72866c1ce12.png)
n this figure we can see the 4 layers of metal. each of these layers are devided in to the "--" lines. lets focus on metal 2 layer. here we assume the routing direction vertical. These "--" lines are called pannels. each pannels assigns the routing guides. here we can see the blue arrows. here routing is heppenes in the even index. it means that intra layer parallel routing. first it is heppenes in the even index and the it will heppen in the odd index. but it is heppening in the parallel in this perticular layer.

So, the (a) figure shows the parallel routing of panels on M2.In (b) figure, we can see the parallel routing of even panels on M3 and (c) shows the parallel routing of odd panels on M3.

#### SKY_L3 - TritonRoute method to handle connectivity

INPUTS: LEF
OUTPUTS:detailed routing solution with optimized wore-length and via count
CONSTRAINTS:Route guide honouring, connectivity constraonts and design rukes
![image](https://user-images.githubusercontent.com/123365818/216902346-a256d06f-057e-4df0-a8f2-04b20270c70a.png)


Now we have to defined the space where detailed routing take spaced.

Handling connectivity
![image](https://user-images.githubusercontent.com/123365818/216902471-a658e85f-9d74-486b-97be-a96e79182fea.png)
o handle the connectivity, two concepts comes into the picture,

Access point:An on-gride point on the metal layer of the route guide, and is used to connect to lower-layer segments, upper-layer segments, pins or IO ports.
Access point cluster (APC): A union of all access points derived from same lower-layer segment,upper-layer guide, a pin or an IO port
Here in the figure shown above, the illustration of access points:

(a)To a lower-layer segment

(b)To a pin shape

(c)To upper layer

Routing topology algorithm and final files list post-route

![image](https://user-images.githubusercontent.com/123365818/216903334-b5f36f23-70fe-463d-98d4-088ae72604cf.png)


The algorithm says that for each APCs we have to find the cost associated with it and we have to do minimum spaning tree betweem the APCs and the cost. finally the conclusion of the algorithm is that we have to find the minimul and the most optimal poits between two APCs.
![image](https://user-images.githubusercontent.com/123365818/216903390-326fadc0-d71e-44ac-8208-3d2c4e0ea8ef.png)

Now, remaning things is the post routing STA analysis. for that the first goal is to extract the perasetic (SPEF). This SPEF extraction is done outside the openlane because openlane does not have SPEF extraction tool.

The .spef file can be found under the routing folder under the results folder.
The following command can be used to stream in the generated GDSII file.

"run_magic"

Now the gds file will be generated and it is stored in the magic folder under results folder.
All commands to run the openlane flow
docker

./flow.tcl -interactive

package require openlane 0.9

prep -design picorv32a

set ::env(SYNTH_STRATEGY) "DELAY 0"

run_synthesis

init_floorplan

place_io

global_placement_or

detailed_placement

tap_decap_or

detailed_placement

run_cts

gen_pdn

run_routing

run_magic
