# Exp-5-Design-and-Implimentation of -Memory-Design-using-Verilog-HDL
# Aim
To design and simulate a RAM,ROM,FIFO using Verilog HDL, and verify its functionality through a testbench in the Vivado 2023.1 environment.
Apparatus Required
Vivado 2023.1
# Procedure
1. Launch Vivado 2023.1
Open Vivado and create a new project.
2. Design the Verilog Code
Write the Verilog code for the RAM,ROM,FIFO
3. Create the Testbench
Write a testbench to simulate the memory behavior. The testbench should apply various and monitor the corresponding output.
4. Create the Verilog Files
Create both the design module and the testbench in the Vivado project.
5. Run Simulation
Run the behavioral simulation to verify the output.
6. Observe the Waveforms
Analyze the output waveforms in the simulation window, and verify that the correct read and write operation.
7. Save and Document Results
Capture screenshots of the waveform and save the simulation logs. These will be included in the lab report.

# Code
# RAM

# Code

```
module ram(
input clk,rst,en,
input [7:0] din,
input [9:0] address,
output reg [7:0] dout);
reg [7:0] ram [1023:0];
always@(posedge clk)
begin
 if(rst)
   dout <= 0;
 else if(en)
   ram[address] <= din;       
 else
   dout <= ram[address];      
end
endmodule
```

# Test bench

```
module ram_tb;
reg clk_t,rst_t,en_t;
reg [7:0] din_t;
reg [9:0] address_t;
wire [7:0] dout_t;
ram dut(.clk(clk_t),.rst(rst_t),.en(en_t),.din(din_t),.address(address_t),.dout(dout_t));
initial
begin
clk_t=1'b0;
rst_t=1'b1;
en_t=1'b0;
din_t=8'd0;
address_t=10'd0;
#100 rst_t=1'b0; 
en_t=1'b1; address_t=10'd955; din_t=8'd45;
#50;
en_t=1'b1;
address_t=10'd1000;
din_t=8'd115;
#50;
en_t=1'b0;
address_t=10'd1000;
#50;
en_t=1'b0;
address_t=10'd955;
#50;
$finish;
end
always #10 clk_t = ~clk_t;
endmodule
```

# Output Waveform

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a664e079-7ed5-4281-9f44-485eaf738a95" />


# ROM

 # Code

```
module rom(
input clk,rst,
input[9:0]address,
output reg [9:0]dout );

reg[7:0] rom[1023:0];

initial 
begin
rom[10'd1000]=8'd100;
rom[10'd1021]=8'd125;
rom[10'd1023]=8'd105;
end
always@(posedge clk)
begin
if(rst)
dout<=8'b0;
else
dout<=rom[address];
end
endmodule
```
 
 # Test bench

```
module rom_tb;
reg clk_t,rst_t;
reg [9:0] address_t;
wire [7:0] dout_t;
rom dut(.clk(clk_t),.rst(rst_t),.address(address_t),.dout(dout_t));
initial
begin
clk_t=1'b0;
rst_t=1'b1;
address_t=10'd0;
#50 rst_t=1'b0;
address_t=10'd1000;
#100;
address_t=10'd1021;
#100
address_t=10'd1023;
#100;
$finish;
end
always #10 clk_t=~clk_t;
endmodule
```

# output Waveform

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/75b1cf20-0853-4fd3-93ca-616b1a6f7dfc" />


 # FIFO

 # write verilog code for FIFO

 ```
module syn_fifo #(parameter DEPTH=8, DATA_WIDTH=8)(
    input clk, rst_n,
    input w_en, r_en,
    input [DATA_WIDTH-1:0] data_in,
    output reg [DATA_WIDTH-1:0] data_out,
    output full, empty
);
    reg [$clog2(DEPTH)-1:0] w_ptr, r_ptr;
    reg [DATA_WIDTH-1:0] fifo [0:DEPTH-1];

    always @(posedge clk) begin
        if (!rst_n) begin
            w_ptr <= 0;
            r_ptr <= 0;
            data_out <= 0;
        end
    end

    always @(posedge clk) begin
        if (w_en & !full) begin
            fifo[w_ptr] <= data_in;
            w_ptr <= w_ptr + 1'b1;
        end
    end

    always @(posedge clk) begin
        if (r_en & !empty) begin
            data_out <= fifo[r_ptr];
            r_ptr <= r_ptr + 1'b1;
        end
    end

    assign full  = ((w_ptr + 1'b1) == r_ptr);
    assign empty = (w_ptr == r_ptr);

endmodule
```

 # Test bench

```
module syn_fifo_tb;

    reg clk, rst_n;
    reg w_en, r_en;
    reg [7:0] data_in;
    wire [7:0] data_out;
    wire full, empty;

    syn_fifo #(8,8) dut (
        .clk(clk),
        .rst_n(rst_n),
        .w_en(w_en),
        .r_en(r_en),
        .data_in(data_in),
        .data_out(data_out),
        .full(full),
        .empty(empty)
    );

    initial clk = 0;
    always #10 clk = ~clk;

    initial begin
        rst_n = 0; w_en = 0; r_en = 0; data_in = 0;
        #25 rst_n = 1;
        @(posedge clk); w_en = 1; data_in = 8'd50;
        @(posedge clk); data_in = 8'd44;
        @(posedge clk); data_in = 8'd45;
        @(posedge clk); w_en = 0;
        @(posedge clk); r_en = 1;
        #50 $finish;
    end

endmodule
```

# output Waveform

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/220baa30-34ae-4306-b662-b7cac15dcd0d" />


# Conclusion
The RAM, ROM, FIFO memory with read and write operations was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the memory operations and observing the output waveforms. The experiment demonstrates how to implement memory operations in Verilog, effectively modeling both the reading and writing processes.
 
 

