# SET - A

## Question

You have two 4 bit inputs A, B, and one 4 bit output Y. You need to check if all the bits of input A and B are equal or not. The bit position where input A and B are not equal, the output at that bit position is zero, and if the bits are equal, it is one. You can check the example below for better understanding.

A = 1100
B = 1010
Y = 1001

**Deliverables**

- Write a verilog code for implementing this system
- Verify the functionality using a vector waveform file in Quartus

## Answer

```verilog
module eval(Y, A, B);
input [3:0] A, B;
output [3:0] Y;

assign Y = A^~B;
endmodule
```

# SET - B

## Question

You have two 4 bit inputs A, B, and one 4 bit output Y. You need to check if all the bits of input A and B are equal or not. The bit position where input A and B are not equal, the output at that bit position is one, and if the bits are equal, it is zero. You can check the example below for better understanding.

A = 1100
B = 1010
Y = 0110

**Deliverables**

- Write a verilog code for implementing this system
- Verify the functionality using a vector waveform file in Quartus

## Answer

```verilog
module eval(Y, A, B);
input [3:0] A, B;
output [3:0] Y;

assign Y = A^B;
endmodule
```