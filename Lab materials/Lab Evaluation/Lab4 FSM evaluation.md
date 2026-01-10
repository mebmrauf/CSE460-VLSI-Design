# Question:

Design a Verilog module of a Mealy-type FSM with inputs w(1 bit), clk, reset and output z(1 bit).
If the input pattern is **1110** or **1111**, then output `z = 1`. (Consider **non-overlapping** input streams)

You are also required to use the **Synchronous reset**. The reset will activate when it is **high**.

| A | 0 | 0 | 1 | 1 | 1 | 0 | 1 | 1 | 1 | 1 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| z | 0 | 0 | 0 | 0 | 0 | **1** | 0 | 0 | 0 | **1** | 0 | 0 |

**Deliverables:**

(a) Draw the state diagram in the question paper
(b) Verilog code of the module
(c) Snap of the final output waveform

**Input guidelines:**

* [ ] clk period = 10 ns
* [ ] Invert the clk
* [ ] Activate reset only for the first cycle
* [ ] Add offset 0.5 ns to reset and input (w)
* [ ] Use the input sequences for input (w)

| 0 | 0 | 1 | 1 | 1 | 0 | 1 | 1 | 1 | 1 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

## Answer
```Verilog
module mealyfsm (Clock, Resetn, w, z,y,Y);

	input Clock, Resetn, w;
	output reg z;
	output reg y, Y;
	parameter A = 2'b00, B = 2'b01, C = 2'b10, D = 2'b11;
	
	
	// Define the next state and output "combinational" circuits
	always @(w, y)
	case (y)
	
		A: 	if (w)
		
			begin
			z = 0;
			Y = B;
			end
			
			else
			begin
			z = 0;
			Y = A;
			end
		
		
		B: 	if (w)
		
			begin
			z = 0;
			Y = C;
			end
			
			else
			begin
			z = 0;
			Y = A;
			end
			
		C: 	if (w)
		
			begin
			z = 0;
			Y = D;
			end
			
			else
			begin
			z = 0;
			Y = A;
			end

		D: 	if (w)
		
			begin
			z = 1;
			Y = A;
			end
			
			else
			begin
			z = 1;
			Y = A;
			end
		default: Y = A;
		
	endcase
	
	
	// Define the sequential block
	always @(posedge Clock) begin
	if (Resetn) y <= A;
	else y <= Y;
	
	end


endmodule
```
