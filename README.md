# SEARCH OF K NEAREST NEIGHBORS

### Abstract
The principle behind kNN is to find predefined number of training samples closest in distance to a new point and predict the label from these.
The number of samples is user input value (K) and can vary upto 16. If the value of K is 1, then the object is assigned to the single nearest neighbor.

![Picture1](https://user-images.githubusercontent.com/89927660/133939437-5ed879e6-d628-4983-b273-de525cdcecd2.png)

Here, if K = 9, algorithm reports 9 nearest neighbors having Euclidean distance from test vector that is represented as red triangle. The blue circles represent the data points in dimensional space. IP was designed to implement kNN algorithm on FPGA board and on its Processing System (PS) for a comparative study. 

### Euclidean Distance
The Euclidean distance between the reference point and rest of the points in the d-dimensional space is given by (kNN runs this formula for computation purpose): 

![Picture1](https://user-images.githubusercontent.com/89927660/133939512-491fe78f-346f-4eec-904b-e6ac4d8be99b.png)
 
By calculating the Euclidean distance, depending on k value, we get k nearest neighbors i.e., the distance between data points for all the training samples which can increase the computation cost. 

### HARDWARE DESCRIPTION: 
The ZYBO (ZYnq BOard) is a feature-rich, ready to use, embedded software and digital circuit development platform build around the smallest member of Xilinx Zynq-7000 family (Z-7010). It is based on Xilinx All Programmable SoC architecture which tightly integrates a dual core ARM Cortex A9 Processor with Xilinx 7 series FPGA logic. When coupled with the set of connectivity peripherals available on ZYBO, the Zynq Z-7010 can host a whole system design. They can be used for designing systems of any complexity, from a bare metal program that controls LEDs to a complete OS running multiple server applications in tandem

![Picture1](https://user-images.githubusercontent.com/89927660/133939817-5de1d219-479c-49eb-adf3-39dd9b9aecde.png)

### ZYNQ ARCHITECTURE:
![Picture1](https://user-images.githubusercontent.com/89927660/133939841-51afbca8-ec6c-40f6-93d9-2e4a97286c6b.png)

The PS side consists of processing resources, cache memory, memory interfaces, interconnect and clock generation circuitry. PS supports software routines. PL side is composed of general purpose FPGA logic fabric - CLBs, IOBs. The Xilinx tools will automatically infer the required LUTs, FFs, IOBs, etc. from the design and map them accordingly. PL section is ideal for implementing high-speed logic, arithmetic and data flow subsystems. The primary interface between PS and PL is via a set of 9 AXI (Advanced eXtensible Interface), each of which is composed of multiple channels. Master is in control of the bus and initiates transaction while slave responds.

### USAGE OF DDR MEMORY: 
DDR as the name stands for Double Data Rate is a type of memory that can transfer data twice as fast as regular dynamic memories. This is because the DDR memory can send and receive signals twice per clock cycle i.e., reading both on rising and falling edges of the clock. It is connected to hard memory controller in the PS part. Datasets with 65k rows can therefore be accessed at a faster rate.

### DESIGN SUMMARY: 
The entire system was designed using Xilinx Vivado Tools. The board used for implementation is Zybo Zynq made by Digilent. 

### 1. VIVADO HIGH LEVEL SYNTHESIS: 
HLS tool converts C/C++/System based designs to RTL design files. It automatically creates the scripts that are required to perform co-simulation of the generated design with original testbench file. In this algorithm code, the input dataset can be user specified and the number of rows, dimensions and number of output K-Neighbors are specified by used during runtime. The kNN function will output both the Index of the neighbor and the Euclidean distance of the neighbor with respect to the reference point. The output in also sorted in order of the Euclidean distance. The code as optimized using insertion sort for better performance and reduced resource uttilization, considering the worse case time complexity. 

![Picture1](https://user-images.githubusercontent.com/89927660/133939934-4495d544-019e-4d5e-8349-15faed906c54.png)

#### _Logic Synthesis_
Code functionality was substantiated in the console and synthesis report was generated to identify the timing and latency measures.

![Picture1](https://user-images.githubusercontent.com/89927660/133940201-87e8b310-64c8-4d2e-9a1f-b78c0f8e07c3.png)

#### _RTL Co-Simulation_
High-Level synthesis can re-use the C test bench to verify the RTL using simulation. Co-Simulation was performed for Verilog and the latency was analyzed. 

![Picture2](https://user-images.githubusercontent.com/89927660/133940205-907a35d3-7f12-4257-a8eb-225f66d70dcf.png)

#### _WaveForm Check_
Using Vivado Wave viewer and the dump trace from the Verilog Co-Simulation, the output of the kNN function was analyzed. The below waveforms show the result of k nearest neighbor’s index values in sorted form for 16-dimension datasets.  

![Picture3](https://user-images.githubusercontent.com/89927660/133940206-6ba884d3-b99c-46e3-9b4c-8d91e48353cd.png)

#### _RTL Export_
The final step in the High-Level Synthesis flow was to package the design as an IP block for use with other tools in the Xilinx Design Suite. The design was exported using VHDL as the language. RTL underwent Synthesis, Placement and Routing evaluations also. Implementation results in Vivado HLS is shown below. 

![Picture4](https://user-images.githubusercontent.com/89927660/133940209-0dbd8298-d2a5-4b6e-a123-32754ca75fa7.png)

### 2. VIVADO IDE DESIGN - IP INTEGRATOR: 
![Picture1](https://user-images.githubusercontent.com/89927660/133939731-6546f2ee-cadd-4058-bc81-6494e7b1f5ec.png)

Since DDR memory lie on the PS part of FPGA, AXI stream interfacing is established between the two devices. The dataset is stored in the DDR. The datasets from DDR is accessed by a core IP – where AXI DMA acts as master and kNN IP acts as slave. The indexes and Euclidean distances of k nearest neighbors are sent from core IP to memory – where kNN IP acts as master and AXI DMA acts as slave. DMA oversees moving the data, without the participation of the processor. This can reduce the clock cycles to read or write a block of data, reducing the overhead of the processor. 

![image](https://user-images.githubusercontent.com/89927660/133939977-0a2b1c49-c7d9-4481-9992-accc0f89c84e.png)

#### _IP Block Diagram_

In Vivado IDE after selecting appropriate board configuration settings, the kNN IP was imported as a User-defined IP. We also added Zynq 7 processing system and other related IPs. The blocks were interconnected using IP integrator designer assistance tool – run block automation/auto-connect and further fine-tuned manually. 

![8](https://user-images.githubusercontent.com/89927660/133940489-d0935f59-7c20-44a2-b3ba-e6fe2cebdb39.png)


#### _Design Validation_
All of the source files for the IP blocks that were used in the IP integrator block diagram, as well as constraint file was generated during the synthesis process. Specified Verilog as the target language when creating the project, all the generated source files were in Verilog language. 

![7](https://user-images.githubusercontent.com/89927660/133940481-b0ef867d-8ade-4476-a735-873099f1816f.png)

#### _The implemented design after synthesis displaying placement and routing results is shown below:_

![6](https://user-images.githubusercontent.com/89927660/133940476-253e7816-d747-419b-b415-35d1e4e5e889.png)

#### _Design Timing Summary_

![5](https://user-images.githubusercontent.com/89927660/133940469-6d830e0d-ba68-473d-ad09-42308e47f7aa.png)

#### _Timing Report_

![4](https://user-images.githubusercontent.com/89927660/133940460-43a524ab-6be5-41de-9ff3-5c050711ab1f.png)

#### _Power Report_

![3](https://user-images.githubusercontent.com/89927660/133940455-815482e8-9356-4385-9b9c-e5e559c04964.png)

#### _Resource Utilization_

![2](https://user-images.githubusercontent.com/89927660/133940453-dc93ad76-9002-479a-87d0-1d1348b741c8.png)

#### _Area Report_

![1](https://user-images.githubusercontent.com/89927660/133940450-d9c668d8-f8b3-4a1e-bd78-ba0d43cdc3d0.png)

### 3. SOFTWARE DEVELOPMENT KIT (SDK): 
SDK provides an environment to create, compile and debug the software applications within one tool. The SDK code was also used to find if there were any mismatch between the output of the hardware and software implementation. To make the system design more user friendly and dynamic, the number of rows, dimensions and k value are variables specified by the user during runtime via the SDK terminal. 

The Bitstream for the design is generated which completes the hardware setup. The bitstream is exported to software environment (SDK) where a software application to control and interact with the custom kNN hardware is developed. 

Speed up is the measure of relative performance of two systems processing the same problem. In this case, we compare the hardware and software implementation using its execution times.

![image](https://user-images.githubusercontent.com/89927660/133940058-458f97a0-29da-4cff-a584-98323f44aeac.png)

![image](https://user-images.githubusercontent.com/89927660/133940539-e0c37656-f487-44fb-bf15-ee3a1592e04d.png)

### FASTER IMPLEMENTATION: 
1.	Pipelining to allow concurrent operations Pipelining can be added to the top function in HLS to set pipeline for the loop. This can be set using the below statement within the loop.

#pragma HLS pipeline II=1

2. In order to speedup data transfer and allow the system to be able to transfer very large datasets, multiple DMAs can be used. Only one DMA is required to have the write channel enabled and all the other only need to have read channel enabled. 

3. Use scatter gather mode
SimpleTransfer mode has a limit of 2 23 bits for its maximum packet length. For datasets with larger size, scatter gather mode can be enabled in the DMA using buffer descriptors placed in BRAMs.

### RESULTS: 
Implemented kNN function in hardware and compared performance with software implemented on ARM core. Results found indicated that that hardware implementation is comparatively faster than the software part. Software allows parallelism property, allowing many modules computing in the same clock cycle. Hence the number of clock cycles is larger than hardware. 

![image](https://user-images.githubusercontent.com/89927660/133940145-0481432c-55d3-44b9-8abd-1648ebde0579.png)

