# Question 1

In an industrial plant, a mixing chamber requires a specific sequence of valve activations to combine reagents safely. The valves are controlled by four commands: **A** (Acid), **B** (Base), **C** (Catalyst), and **D** (Diluent).

Design and implement a synchronous Finite State Machine (FSM) that monitors the sequence of commands and triggers an alarm signal () only when the sequence **BBAA** is completed.

> **Note:** In your digital implementation, map these to a 2-bit binary input (**A**=00, **B**=01, **C**=10, **D**=11).

| Input (w) | A | B | A | A | B | B | A | A |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Output (z)** | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |

**Deliverables:**

1. Draw the state diagram in the question paper.
2. Verilog code of the module.
3. Snap of the final output waveform.

### Input guidelines:

* [ ] clk period = 10 ns
* [ ] Invert the clk
* [ ] Activate reset only for the first cycle
* [ ] Add offset 0.5 ns to reset and input (w)
* [ ] Use the input sequences for input (w)

| Sequence | D | A | A | B | B | A | A | C |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |

## Answer

```Verilog
module fsm (clk, reset, w, z, y, Y);
    input clk, reset;
    input [1:0] w;
    output reg z;

    parameter S0 = 2'b00;
    parameter S1 = 2'b01;
    parameter S2 = 2'b10;
    parameter S3 = 2'b11;

    output reg [1:0] y, Y;

    always @(*) begin
        case (y)
            S0:
                if (w == 2'b01)
                begin
                    Y = S1;
                    z = 0;
                end 
                else
                begin
                    Y = S0;
                    z = 0;
                end

            S1:
                if (w == 2'b01)
                begin
                    Y = S2;
                    z = 0;
                end
                else
                begin
                    Y = S0;
                    z = 0;
                end

            S2:
                if (w == 2'b00)
                begin
                    Y = S3;
                    z = 0;
                end
                else if (w == 2'b01) 
                begin
                    Y = S2;
                    z = 0;
                end
                else
                begin
                    Y = S0;
                    z = 0;
                end

            S3:
                if (w == 2'b00)
                begin
                    Y = S0;
                    z = 1;
                end
                else if (w == 2'b01)
                begin
                    Y = S1;
                    z = 0;
                end
                else
                begin
                    Y = S0;
                    z = 0;
                end

            default: Y = S0;
        endcase
    end

    always @(posedge clk or posedge reset) begin
        if (reset)
            y <= S0;
        else
            y <= Y;
    end

endmodule
```

# Question 2
You have to design a vending machine in Quartus where the following products are available:

* Chocolate (4 tk)
* Ice cream (9 tk)
* Orange juice (90 tk)
* Mango juice (130 tk)

**Specifications:**

The vending machine has 4 shelves. Shelf 1 contains chocolate, shelf 2 ice cream, shelf 3 orange juice and shelf 4 has mango juice. When a user inputs the shelf number, the product price **PV** gets selected accordingly. Shelf number, user's money, returned money by the machine and product bought condition are represented as **shelf** (input), **cash_in** (input), **return** (output), and **purchase** (1-bit output), respectively.

The vending machine can only accept the following inputs: 0 tk, 1 tk, 2 tk, 5 tk, 10 tk, 20 tk, 50 tk and 100 tk. Once an acceptable input is more than or equal to the selected product price, the machine generates an output (**purchase=1**), returns to the initial state, and returns the change (if required).

**Deliverables:**

1. Identify the minimum required bit length for the **cash_in**, **PV**, **MV** and **return** variables. [2]
2. Write the Verilog code to implement the described device functionality. [4]
3. Run the simulation, and verify your answer with the following inputs: [4]
* **shelf= 2** (ice-cream) : give 2 tk, 5 tk and 5 tk as cash inputs
* **shelf= 4** (Mango juice) : give 20 tk, 20 tk and 50 tk as cash inputs

## Answer

```Verilog
module vendingmachine(shelf, cash_in, Reset, Clock, purchase, Tk_Cl, cash_return, MV, y, Y, PV);

input [1:0] shelf;
input [2:0] cash_in;
input Reset, Clock;
output reg purchase;
output reg [6:0] cash_return;  // 7-bit is enough (max 130)
output reg Y = 0;
output reg y;
output reg [6:0] MV;            // 7-bit is enough
output reg [6:0] PV;            // 7-bit is enough
output reg [6:0] Tk_Cl = 0;     // 7-bit is enough

parameter S0 = 0, S1 = 1;
parameter T0 = 3'b000, T1 = 3'b001, T2 = 3'b010, T5 = 3'b011, T10 = 3'b100, T20 = 3'b101, T50 = 3'b110, T100 = 3'b111;

// Product price selection based on shelf
always @(*) begin
    case (shelf)
        2'd1: PV = 7'd4;
        2'd2: PV = 7'd9;
        2'd3: PV = 7'd90;
        2'd4: PV = 7'd130;
        default: PV = 0;
    endcase
end

// Next state logic and output value
always @(y, Tk_Cl, cash_in, MV, PV) begin
    case (cash_in)
        T1:   Tk_Cl = 1;
        T2:   Tk_Cl = 2;
        T5:   Tk_Cl = 5;
        T10:  Tk_Cl = 10;
        T20:  Tk_Cl = 20;
        T50:  Tk_Cl = 50;
        T100: Tk_Cl = 100;
        T0:   Tk_Cl = 0;
    endcase

    case (y)
        S0: begin
            if (Tk_Cl >= PV) begin
                if (PV == 0) begin
                    Y = S0;
                    purchase = 0;
                    cash_return = Tk_Cl;
                end else begin
                    purchase = 1;
                    cash_return = Tk_Cl - PV;
                    Y = S0;
                end
            end else if (Tk_Cl == 0) begin
                purchase = 0;
                cash_return = 0;
                Y = S0;
            end else begin
                purchase = 0;
                cash_return = 0;
                Y = S1;
            end
        end

        S1: begin
            if ((MV + Tk_Cl) >= PV) begin
                purchase = 1;
                cash_return = (MV + Tk_Cl - PV);
                Y = S0;
            end else if (Tk_Cl == 0) begin
                purchase = 0;
                cash_return = MV;
                Y = S0;
            end else begin
                purchase = 0;
                cash_return = 0;
                Y = S1;
            end
        end
    endcase
end

// State change with clock edge
always @(posedge Reset, posedge Clock) begin
    if (Reset == 1) begin
        y <= 0;
        MV <= 0;
    end else begin
        if (purchase | Tk_Cl == 0)
            MV <= 0;
        else
            MV <= MV + Tk_Cl;
        y <= Y;
    end
end

endmodule
```
