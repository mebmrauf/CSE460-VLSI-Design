# Question 
Design a 1-bit ALU that performs AND, OR, ADD and SUBTRACT operations. The ALU will have two one-bit inputs (a,b) and one two-bit control signal (S). It will work like a 4x1 mux. When the control signal S = 00, the output will be the AND of both inputs. When S= 01, the output will be the OR of both inputs. When S = 10, the output will be the ADD of both inputs. When S = 11, the output will be the SUBTRACT of both inputs.

**Input Guidelines:**

```
00 ns to 10 ns: S = 00, a = 1, b = 1
10 ns to 20 ns: S = 01, a = 0, b = 1
20 ns to 30 ns: S = 10, a = 1, b = 0
30 ns to 40 ns: S = 11, a = 1, b = 0
```

**Deliverables:**

(i) Copy and paste the Verilog HDL code.

(ii) Screenshot of the timing diagram.(With the input guidelines)

## Answer

```verilog
module ALU(a, b, S, y);
input a,b;
input [1:0] S;
output reg  y;

always @(*)
begin
    case (S)
        2'b00: y = a & b;
        2'b01: y = a | b;
        2'b10: y = a + b;
        2'b11: y = a - b;
        default: y = 0;
    endcase
end

endmodule
```