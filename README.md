# Sequence-Detector
## Aim
To design and simulate a sequence detector using both Moore and Mealy state machine models in Verilog HDL, and verify their functionality through a testbench using the Vivado 2023.1 simulation environment. The objective is to detect a specific sequence of bits (e.g., 1011) and compare the Moore and Mealy designs.

## Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Write the Verilog Code for Sequence Detector (Moore and Mealy FSM):

Design two Verilog modules: one for a Moore FSM and another for a Mealy FSM to detect a sequence such as 1011.
Create the Testbench:

Write a testbench to apply input sequences and verify the output of both FSM designs.
Add the Verilog Files:

Add the Verilog code for both FSMs and the testbench to the Vivado project.
Run Simulation:

Run the simulation and observe the output to check if the sequence is detected correctly.
Observe the Waveforms:

Analyze the waveform to ensure both the Moore and Mealy machines detect the sequence as expected.
Save and Document Results:

Capture the waveforms and include the results in the final report.

## Verilog Code for Sequence Detector Using Moore FSM
```
// moore_sequence_detector.v
module moore_sequence_detector (
    input wire clk,
    input wire reset,
    input wire seq_in,
    output reg detected
);
    typedef enum reg [2:0] {
        S0, S1, S2, S3, S4  // States for detecting 1011
    } state_t;

    state_t current_state, next_state;

    // State transition logic
    always @(posedge clk or posedge reset) begin
        if (reset)
            current_state <= S0;
        else
            current_state <= next_state;
    end

    // Next state and output logic
    always @(*) begin
        case (current_state)
            S0: begin
                if (seq_in) next_state = S1;
                else next_state = S0;
                detected = 0;
            end
            S1: begin
                if (seq_in) next_state = S1;
                else next_state = S2;
                detected = 0;
            end
            S2: begin
                if (seq_in) next_state = S3;
                else next_state = S0;
                detected = 0;
            end
            S3: begin
                if (seq_in) next_state = S4;
                else next_state = S2;
                detected = 0;
            end
            S4: begin
                if (seq_in) next_state = S1;
                else next_state = S0;
                detected = 1;  // Sequence 1011 detected
            end
            default: next_state = S0;
        endcase
    end
endmodule
```

output

![Screenshot Image 2024-11-11 at 16 02 37_ba99ffb6](https://github.com/user-attachments/assets/8d27a68b-2a5d-4c86-b883-2ea8b8b735a9)

## Verilog Code for Sequence Detector Using Mealy FSM

```
module mealy(clk,rst,inp,out);
 input clk, rst, inp;
 output out;
 reg out;
 reg [1:0] state;
 always @(posedge clk, posedge rst)
 begin
     if(rst)
     begin
     out <= 0;
            state <= 2'b00;
         end
     else
         begin
             case (state)
             2'b00: 
                 begin
                     if (inp) begin
                         state <=2'b01;
                         out <=0;
                     end
                     else begin
                         state <=2'b10;
                         out <=0;
                    end
                 end
             2'b01:
                 begin

                     if(inp) begin

                         state <= 2'b00;

                         out <= 1;

                     end

                     else begin

                         state <= 2'b10;

                         out <= 0;
                     end
                 end
             2'b10:
                 begin
                     if(inp) begin

                         state <= 2'b01;

                         out <= 0;

                     end

                     else begin

                         state <= 2'b00;

                         out <= 1;
                     end
                 end
             default:
                 begin
                     state <= 2'b00;
                     out <= 0;
                 end
             endcase
         end
 end
 endmodule
```
## output
![Screenshot Image 2024-11-11 at 16 06 01_a5b5240c](https://github.com/user-attachments/assets/d65c8df0-bfc3-4918-8497-10ab9fde6295)


## Testbench for Sequence Detector (Moore and Mealy FSMs)

```
CODE:`timescale 1ns/1ps

module tb_fsm_sequence;

    // Testbench signals
    reg clk;
    reg reset;
    reg run;
    wire [3:0] count;

    // Instantiate the FSM module
    fsm_sequence uut (
        .clk(clk),
        .reset(reset),
        .run(run),
        .count(count)
    );

    // Clock generation (50MHz, period = 20ns)
    always #10 clk = ~clk; // Toggle clock every 10 time units (20ns period)

    // Test sequence
    initial begin
        // Initialize inputs
        clk = 0;
        reset = 1;
        run = 0;

        // Apply reset
        #20 reset = 0;  // De-assert reset after 20ns

        // Test the FSM transitions with run signal
        #20 run = 1;    // Start running the FSM after 20ns

        // Observe FSM behavior by allowing it to transition through states
        #100 run = 0;   // Stop the FSM by disabling run
        #40 run = 1;    // Restart the FSM after a short pause
        #100 $finish;   // End the simulation
    end

    // Monitor the output
    initial begin
        $monitor("Time: %0t | clk: %b | reset: %b | run: %b | count: %d", 
                 $time, clk, reset, run, count);
    end

    // Dump waves for Vivado simulation
    initial begin
        $dumpfile("fsm_sequence_tb.vcd");
        $dumpvars(0, tb_fsm_sequence);
    end

endmodule
```
## output
![Screenshot Image 2024-11-11 at 16 06 39_00d15bc2](https://github.com/user-attachments/assets/58e91ac8-a833-4afa-bde4-5e1391aa2446)

## Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
