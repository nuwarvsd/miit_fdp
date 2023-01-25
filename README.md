# miit_fdp
Day 1 - Introduction to Verilog RTL design and Synthesis

Introduction to iverilog design test bench

Simulator: tool used for simulating the design.
RTL Design is checked for adherence to the spec by simulating the design.
Design : the actual verilog code or set of verilog codes which has the intended functionality to meet with the required specifications.
![image](https://user-images.githubusercontent.com/123365818/214258651-1ecf6b07-b6f8-424e-95fd-ef2c657e2ea2.png)

Test_bench: the setput to apply the stimulus (test vector) to the design to check its functionality
![image](https://user-images.githubusercontent.com/123365818/214258781-80db0540-cd8c-499a-b1b2-016e08af44da.png)

Labs using iverilog and gtkwave
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

Introduction to Yosys and Logic synthesis
Synthesizer :It is a tool used for the conversion of an RTL to a netlist.
Netlist : It is a representation of the input design to yosys in terms of standard cells present in the library. Yosys is the Synthesizer tool that we will be using. Diiferent levels of abstraction and synthesis
![image](https://user-images.githubusercontent.com/123365818/214261468-9e7ad23e-3e6d-4e9a-9f81-c5c4c9fdc200.png)
![image](https://user-images.githubusercontent.com/123365818/214261856-9ce803da-34c5-4429-9b22-0a3ffe34da54.png)
During Logic Synthesis an RTL code is transformed into a gate level netlist.

A synthesizer performs gate level translation.
Design is converted into gates and connections are made between the gates.
The file what we write out finally is a gate-level netlist.
.lib is the collection of logic modules which includes basic logic gates such as AND, OR, NOT, etc. It also contains different flavours of the same gate.
Setup time Analysis
It should take 1 CLK cycle for the signal to propagate from the Launch DFF-A to the Capture DFF-B through the combimational circuit.
While this propagation all delays : Propagation delay of DFF A (Tcq-a)+ Propagation delay of combinational circuit(Tcomb)+Setup time of DFF-B(Tsetup_b). Thus ,the constraint becomes:

Tclk>Tcq-a+Tcomb+Tsetup_b
Setup time is the time for which data should arrive before the launch clock edge to be reflected at the output reliably.
For high performance we need high speed gates.So, The frequency of a gates should be high or the Tclk should be less . So all the above mentioned delay should be less. 

 ![image](https://user-images.githubusercontent.com/123365818/214262680-103e1268-c5e2-4fce-8b6a-8188aacfc182.png)
 Hold up time Analysis
After the after the edge of the clock I don't want data to change . So, the fastest change that can happen at the input of we would be after the hold time window so that I can reliably capture the previous clock edge of launch flop DFF-A to ensure there are no hold issues at DFF-B . 
 Thold_b<Tcq-a+Tcomb
 ![image](https://user-images.githubusercontent.com/123365818/214262923-574ba77b-3371-4f87-be73-2138b30196c2.png)

Comparison of Faster Cells and Slower Cells
The load for a cell in a digital logic circuit usually is taken as a capacitor.
Propagation delay is the time required for the input to be reflected in the output of the cell it depends on the gate driving capacity which is dependent on the capacitance. Faster the charging and discharging of a capacitance, lesser will be the cell delay. In order to charge/discharge the capacitance fast, we need transistors which are capable of sourcing more current which means it is a wide transistor as the current carrying capacity of a transistor is the function of its width. Wider the transistors, lower will be the delay but more is the area and power. Narrow Transistors will have more delay but less area and less power.

So fastercells don't come free. They come at the tradeoffs of area and power.
Labs using Yosys and Sky130 PDKs
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

Commands to write a netlist

write_verilog -noattr good_mux_netlist.v
!vim  good_mux_netlist.v

![yosisnetlist](https://user-images.githubusercontent.com/123365818/214264824-22b5ea78-c964-42f2-b6d9-c08ba47ef3b6.PNG)

Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

SKY130RTL D2SK1 - Introduction to timing .libs

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
It can be compared between the and gates.

![Captureand1](https://user-images.githubusercontent.com/123365818/214483776-d5022338-f901-4165-b817-9d36b83c7b6a.PNG) ![Captureand2](https://user-images.githubusercontent.com/123365818/214483964-622f7224-532a-4e65-976d-063628b7cfb5.PNG)
