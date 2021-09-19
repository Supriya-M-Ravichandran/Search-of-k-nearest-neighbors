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

### DESIGN SUMMARY: 
The entire system was designed using Xilinx Vivado Tools. The board used for implementation is Zybo Zynq made by Digilent. 

### 1. VIVADO HIGH LEVEL SYNTHESIS: 
HLS tool converts C/C++/System based designs to RTL design files. It automatically creates the scripts that are required to perform co-simulation of the generated design with original testbench file. In this algorithm code, the input dataset can be user specified and the number of rows, dimensions and number of output K-Neighbors are specified by used during runtime. The kNN function will output both the Index of the neighbor and the Euclidean distance of the neighbor with respect to the reference point. The output in also sorted in order of the Euclidean distance. The code as optimized using insertion sort for better performance and reduced resource uttilization, considering the worse case time complexity. 

![Picture1](https://user-images.githubusercontent.com/89927660/133939934-4495d544-019e-4d5e-8349-15faed906c54.png)

![image](https://user-images.githubusercontent.com/89927660/133939957-13b759bb-77e6-423e-9b3f-81a8fe75ea15.png)

![image](https://user-images.githubusercontent.com/89927660/133939961-8dec1d83-42f7-496b-b1db-89a55d6edf8d.png)

![image](https://user-images.githubusercontent.com/89927660/133939964-6ab0317d-b02a-476d-b7fb-204a23ffbde5.png)

![image](https://user-images.githubusercontent.com/89927660/133939970-dc0684ec-3ace-4ff2-835c-15cbca4eb7d5.png)

### 2. VIVADO IDE DESIGN - IP INTEGRATOR: 
![Picture1](https://user-images.githubusercontent.com/89927660/133939731-6546f2ee-cadd-4058-bc81-6494e7b1f5ec.png)

Since DDR memory lie on the PS part of FPGA, AXI stream interfacing is established between the two devices. The dataset is stored in the DDR. The datasets from DDR is accessed by a core IP – where AXI DMA acts as master and kNN IP acts as slave. The indexes and Euclidean distances of k nearest neighbors are sent from core IP to memory – where kNN IP acts as master and AXI DMA acts as slave. DMA oversees moving the data, without the participation of the processor. This can reduce the clock cycles to read or write a block of data, reducing the overhead of the processor. 

![image](https://user-images.githubusercontent.com/89927660/133939977-0a2b1c49-c7d9-4481-9992-accc0f89c84e.png)

![image](https://user-images.githubusercontent.com/89927660/133939979-fd37124b-c63c-40ec-81ec-18b6281da612.png)

![image](https://user-images.githubusercontent.com/89927660/133939990-3a85f481-23ee-4ce6-b78d-d51d0edad14f.png)

![image](https://user-images.githubusercontent.com/89927660/133939992-eb15ebf4-fdef-4775-a277-8823ceabc4b8.png)

![image](https://user-images.githubusercontent.com/89927660/133939997-982b7766-40bc-4043-9190-90f2fdbc9db0.png)

![image](https://user-images.githubusercontent.com/89927660/133940003-26f6f38c-84f0-4c7f-9c08-38952e5fdddc.png)

![image](https://user-images.githubusercontent.com/89927660/133940008-5955a9cc-e431-443a-b411-e7b2e77173b2.png)

![image](https://user-images.githubusercontent.com/89927660/133940010-a731e328-fd61-4034-bff1-3d1e00289f1b.png)

![image](https://user-images.githubusercontent.com/89927660/133940015-c9526fbf-2613-426b-8f3e-29ab9b7fb844.png)

### 3. SOFTWARE DEVELOPMENT KIT (SDK): 
SDK provides an environment to create, compile and debug the software applications within one tool. The SDK code was also used to find if there were any mismatch between the output of the hardware and software implementation. To make the system design more user friendly and dynamic, the number of rows, dimensions and k value are variables specified by the user during runtime via the SDK terminal. 

The Bitstream for the design is generated which completes the hardware setup. The bitstream is exported to software environment (SDK) where a software application to control and interact with the custom kNN hardware is developed. 

Speed up is the measure of relative performance of two systems processing the same problem. In this case, we compare the hardware and software implementation using its execution times.

![image](https://user-images.githubusercontent.com/89927660/133940058-458f97a0-29da-4cff-a584-98323f44aeac.png)
![image](https://user-images.githubusercontent.com/89927660/133940060-7346b054-3e77-407d-9c5e-afb8d714fa4a.png)

### HARDWARE DESCRIPTION: 
The ZYBO (ZYnq BOard) is a feature-rich, ready to use, embedded software and digital circuit development platform build around the smallest member of Xilinx Zynq-7000 family (Z-7010). It is based on Xilinx All Programmable SoC architecture which tightly integrates a dual core ARM Cortex A9 Processor with Xilinx 7 series FPGA logic. When coupled with the set of connectivity peripherals available on ZYBO, the Zynq Z-7010 can host a whole system design. They can be used for designing systems of any complexity, from a bare metal program that controls LEDs to a complete OS running multiple server applications in tandem

![Picture1](https://user-images.githubusercontent.com/89927660/133939817-5de1d219-479c-49eb-adf3-39dd9b9aecde.png)

### ZYNQ ARCHITECTURE:
![Picture1](https://user-images.githubusercontent.com/89927660/133939841-51afbca8-ec6c-40f6-93d9-2e4a97286c6b.png)

The PS side consists of processing resources, cache memory, memory interfaces, interconnect and clock generation circuitry. PS supports software routines. PL side is composed of general purpose FPGA logic fabric - CLBs, IOBs. The Xilinx tools will automatically infer the required LUTs, FFs, IOBs, etc. from the design and map them accordingly. PL section is ideal for implementing high-speed logic, arithmetic and data flow subsystems. The primary interface between PS and PL is via a set of 9 AXI (Advanced eXtensible Interface), each of which is composed of multiple channels. Master is in control of the bus and initiates transaction while slave responds.

### USAGE OF DDR MEMORY: 
DDR as the name stands for Double Data Rate is a type of memory that can transfer data twice as fast as regular dynamic memories. This is because the DDR memory can send and receive signals twice per clock cycle i.e., reading both on rising and falling edges of the clock. It is connected to hard memory controller in the PS part. Datasets with 65k rows can therefore be accessed at a faster rate.

### FASTER IMPLEMENTATION: 
1.	Pipelining to allow concurrent operations Pipelining can be added to the top function in HLS to set pipeline for the loop. This can be set using the below statement within the loop.

#pragma HLS pipeline II=1

2. In order to speedup data transfer and allow the system to be able to transfer very large datasets, multiple DMAs can be used. Only one DMA is required to have the write channel enabled and all the other only need to have read channel enabled. 

3. Use scatter gather mode
SimpleTransfer mode has a limit of 2 23 bits for its maximum packet length. For datasets with larger size, scatter gather mode can be enabled in the DMA using buffer descriptors placed in BRAMs.

Implemented kNN function in hardware and compared performance with software implemented on ARM core. Results found indicated that that hardware implementation is comparatively faster than the software part. Software allows parallelism property, allowing many modules computing in the same clock cycle. Hence the number of clock cycles is larger than hardware. 
