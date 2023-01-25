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
 
 trace the transitions for selected variable
 
![Capture5](https://user-images.githubusercontent.com/123365818/214260812-0dd6e85b-1356-4b0e-be35-db88f1193698.PNG)

View our Verilog RTL design and testbench code using

vim tb_good_mux.v -o good_mux.v

![Capturevim1](https://user-images.githubusercontent.com/123365818/214261023-1eaa6f6d-e572-4fa6-8e19-29f078106265.PNG)

  ### Introduction to Yosys and Logic synthesis
Synthesizer :It is a tool used for the conversion of an RTL to a netlist.
Netlist : It is a representation of the input design to yosys in terms of standard cells present in the library. Yosys is the Synthesizer tool that we will be using. Diiferent levels of abstraction and synthesis
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
Setup time is the time for which data should arrive before the launch clock edge to be reflected at the output reliably.
For high performance we need high speed gates.So, The frequency of a gates should be high or the Tclk should be less . So all the above mentioned delay should be less. 

 ![image](https://user-images.githubusercontent.com/123365818/214262680-103e1268-c5e2-4fce-8b6a-8188aacfc182.png)
    #### Hold up time Analysis
After the after the edge of the clock I don't want data to change . So, the fastest change that can happen at the input of we would be after the hold time window so that I can reliably capture the previous clock edge of launch flop DFF-A to ensure there are no hold issues at DFF-B . 
 Thold_b<Tcq-a+Tcomb
 ![image](https://user-images.githubusercontent.com/123365818/214262923-574ba77b-3371-4f87-be73-2138b30196c2.png)

   #### Comparison of Faster Cells and Slower Cells
The load for a cell in a digital logic circuit usually is taken as a capacitor.
Propagation delay is the time required for the input to be reflected in the output of the cell it depends on the gate driving capacity which is dependent on the capacitance. Faster the charging and discharging of a capacitance, lesser will be the cell delay. In order to charge/discharge the capacitance fast, we need transistors which are capable of sourcing more current which means it is a wide transistor as the current carrying capacity of a transistor is the function of its width. Wider the transistors, lower will be the delay but more is the area and power. Narrow Transistors will have more delay but less area and less power.

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

# Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

  ## SKY130RTL D2SK1 - Introduction to timing .libs

To study the name of the library

Command: vim ../my_lib/lib/SKY130_fd_sc_hd__tt_025C_1v80.lib
![Capture1](https://user-images.githubusercontent.com/123365818/214477368-67cff53e-f4f0-47c2-9ce5-efe06a096bf4.PNG)

TT : it stands for typical.

The important three parameters of the library:

P- Process:The variations due to fabrication
V -Voltage: Variations in voltage also impact the behavior of the circuit
T- Temperature :Semiconductors are very sensitive to temperature variations too. All this variations determine the performance of a silicon whether it is fast or slow. 

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

![Captureand1](https://user-images.githubusercontent.com/123365818/214483776-d5022338-f901-4165-b817-9d36b83c7b6a.PNG) ![Captureand2](https://user-images.githubusercontent.com/123365818/214483964-622f7224-532a-4e65-976d-063628b7cfb5.PNG)
![Captureand4](https://user-images.githubusercontent.com/123365818/214484733-59462614-1885-4b62-ba5d-11121ecb8fc5.PNG)
According to the comparison, the area of the and gate "and2_4" is larger than the area of the and gate "and2_2" which in turn has more area with the and gate "and2_0". because of and2_4 employs wider transistors. These are the different flavours of the same and gate. And and2_4 being the widest also has large leakage power values as well as large area. But it will have small delay values as it is faster.

  ## SKY130RTL D2SK2 - Hierarchical vs Flat Synthesis
To understand the multiple modules are present while synthesizing, the sysnthesis can be done in two forms
![hier mult](https://user-images.githubusercontent.com/123365818/214486111-d5280aa5-cb0e-4d30-8fbb-db8ca2f03798.PNG)
sub_module 1 and sub_module 2 can be seen in above vim window.
It has two some moduels. The module 1 is an OR gate ,sub module 2 is AND gate. The sub module called multiple modules instantiates sub module 1 as u1 and sub module 2 as u2. It has three inputs a b c and an output y.
![Capturesubmodule1](https://user-images.githubusercontent.com/123365818/214487248-f6a0ec7b-49db-46e5-9825-4b3d77015606.PNG)
![Capturemultiplesynth](https://user-images.githubusercontent.com/123365818/214487314-75fe8ac0-f762-4262-ad22-56d88a9a6dd0.PNG)
It also specifies the type and number of cells in a synthesis of RTL design.

![Capturesynthesis](https://user-images.githubusercontent.com/123365818/214487668-e67df9dc-8ddf-49c2-8da5-e1c1e565207b.PNG)
The report has inferred submodule1 having one AND gate ,submodule2 to having one OR gate and multiple module having two cells . 
Now this design is linked to the library using abc command. 
To display the graphical version ,

show multiple_modules
On execution of show command,a synthesized implementation of multiple_modules design is visible.

![Captureshow](https://user-images.githubusercontent.com/123365818/214488480-a3e3e6f2-18d4-4f1a-aa5b-f27f81e918c3.PNG)
Instead of or and and gates it shows the instances u1 and u2 while preserving the hierarchy. This is called the hierarchical design.
![yosyswritenetlist](https://user-images.githubusercontent.com/123365818/214488903-fc185813-6999-419a-9630-bb1e8b93ed51.PNG)
![Yousys](https://user-images.githubusercontent.com/123365818/214490023-67436daf-6bc2-4ede-93a9-6062f6358735.PNG)
![yosysnetlistgenerate](https://user-images.githubusercontent.com/123365818/214489769-4f6a896e-0490-4db4-9063-59654ed8b72d.PNG)
Instead of or, and the circuit is implemented using nand and inverter gates. 
it is always prefered stacked NMOS's(nand gates)to stacked the PMOS's(nor cascaded with inverter for or). Because pmos has a very poor mobility and therefore they have to be made quite wide to obtain a good logical effort .
It can be used flatten to generate a flat netlist. 
flatten
 write_verilog -noattr multiple_modules_flatten_netlist.v
![flatten](https://user-images.githubusercontent.com/123365818/214491064-8620c421-e6cf-4a94-bf50-89b2cf7c32cc.PNG)
![flatten1](https://user-images.githubusercontent.com/123365818/214491394-f59ff1fb-4cb1-4397-ac6c-7334563918b6.PNG)
Even in the design view using show command, it can be seen that it simply displays the structure completely without any hierarchy.
show multiple_modules
![flatten3](https://user-images.githubusercontent.com/123365818/214492017-97018b7a-f94c-4eaa-98c7-9064daa276db.PNG)
Here there are no instances of U1 and U2 and hierarchy is not present.

   #### Why Flops and Flop coding styles

More the combinational circuits can occur more glitch in the output . 
That is why an element is needed to store the value of the output and that element is called FLOP(storage element).Flop provides resistance to glitches as they transition only at the clock edges. Even though the input of the flop is glitching ,the output will be stable. This avoids glitch propagation in further combinational circuits. 

![image](https://user-images.githubusercontent.com/123365818/214586866-5b036ac8-707a-40db-9bf1-577cf7a75f4e.png)
![image](https://user-images.githubusercontent.com/123365818/214587086-0007299d-7b01-4b2b-90e7-697873467af6.png)



     ###### what is glitch?
Glitches are the unwanted or unexpected transactions that occur due to propagation delays in digital circuits. Glitches occur when an input signal changes state ,provided the signal takes two or more paths for circuit and both paths have unequal delays. The higher delay on one of the parts can cause a glitch when the single pass arrive at the output gate.


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

  ### OPTIMISATIONS

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


