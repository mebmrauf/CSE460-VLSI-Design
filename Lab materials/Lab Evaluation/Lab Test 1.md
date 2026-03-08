## Problem 1

Suppose you need to design a circuit for implementing a 3 bit minority function. That is, the output resembles the minority of the inputs, if the minority of inputs is 1, output becomes 1, and if the minority of inputs is low, output is 0. of inputs is low, output is 0.

(i) Build the truth table and determine the logic function using K-map.

(1) Draw the schematic representation of the circuit in DSCH2 using CMOS Technology.

(ii) Verify the functionality of the circuit. You need to show output for all input combinations.

## Problem 2

Design a 4 to 2-bit priority encoder with a control signal to dynamically change the priority sequence. When the control signal (PS) is high (1), the priority sequence should be: 1 > 2 > 0 > 3. When the control signal (PS) is low (0), the priority sequence should be: 3 > 0 > 2 > 1.

The priority encoder takes a 4-bit input as well control signal (PS) and generates a 2-bit output. It also takes a one-bit control signal.

**Input Guidelines**

```
00 ns to 10 ns: PS = 1, w[3]=1, w[2]=1, w[1]=1, w[0]=1
10 ns to 20 ns: PS = 1, w[3]=0, w[2]=0, w[1]=1, w[0]=1
20 ns to 30 ns: PS = 0, w[3]=1, w[2]=1, w[1]=1, w[0]=1
30 ns to 40 ns: PS = 0, w[3]=0, w[2]=0, w[1]=1, w[0]=1
```

**Deliverables:**

(i) Draw truth table for both sequences separately.

(ii) Copy and paste the Verilog HDL code.

(iii) Screenshot of the timing diagram.


```verilog
module priority_encoder_4to2 (
    input  [3:0] w,   // input lines
    input  PS,        // priority select
    output reg [1:0] y
);

always @(*) begin
    if (PS == 1'b1) begin
        // Priority: 1 > 2 > 0 > 3
        if (w[1])       y = 2'b01;
        else if (w[2])  y = 2'b10;
        else if (w[0])  y = 2'b00;
        else if (w[3])  y = 2'b11;
        else            y = 2'b00;
    end
    else begin
        // Priority: 3 > 0 > 2 > 1
        if (w[3])       y = 2'b11;
        else if (w[0])  y = 2'b00;
        else if (w[2])  y = 2'b10;
        else if (w[1])  y = 2'b01;
        else            y = 2'b00;
    end
end

endmodule
```
