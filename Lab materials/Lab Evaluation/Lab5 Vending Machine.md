# Question:

You have to design a **vending machine** in Quartus where the following product is available:

* Chips (35 Tk)

**Specifications:**

User's money, returned money by the machine, product price and product bought condition are represented as `cash_in`, `PV`, `return` and `purchase` respectively. The vending machine can only accept the following inputs: 0 tk, 10 tk, 20 tk, 50 tk and 100 tk. Once an acceptable input is more than or equal to the product price, the machine generates an output (purchase=1), returns to the initial state and returns the change (if required).

**Deliverables:**

i. Identify the minimum required bit length for the `cash_in`, `PV`, `MV` and `return` variables. [2]
ii. Write the Verilog code to implement the described device functionality. [4]
iii. Run the simulation, and verify your answer with the following inputs:[4]

* Give 10 tk, 20 tk and 20 tk as cash inputs
* Give 20 tk, 20 tk as cash inputs

## Answer
```Verilog
module l2t2(cash_in, Reset, Clock, purchase, Tk_Cl, cash_return, MV, y, Y, PV);
    input Reset;
    input Clock;
    input [2:0] cash_in;    // 0,10,20,50,100 tk input
    output reg purchase;    // 1 when product is bought
    output reg [7:0] cash_return;  // money returned

    // Internal registers
    reg [7:0] Tk_Cl = 0;   // Cash inserted in current clock
    reg [7:0] MV = 0;      // Total money inserted
    reg [7:0] PV = 35;     // Product price (Chips 35 Tk)
    reg y = 0;             // Current state
    reg Y;                 // Next state

    // State encoding
    parameter S0 = 0; // Idle / waiting
    parameter S1 = 1; // Accumulating cash

    // Cash input mapping
    parameter T0 = 3'b000;
    parameter T10 = 3'b010;
    parameter T20 = 3'b011;
    parameter T50 = 3'b100;
    parameter T100 = 3'b101;

    // Map cash_in code to actual Tk value
    always @(*) begin
        case(cash_in)
            T10:  Tk_Cl = 10;
            T20:  Tk_Cl = 20;
            T50:  Tk_Cl = 50;
            T100: Tk_Cl = 100;
            default: Tk_Cl = 0;
        endcase
    end

    // Next state logic
    always @(y, Tk_Cl, MV) begin
        case (y)
            S0: begin
                if (Tk_Cl >= PV) begin
                    purchase = 1;
                    cash_return = Tk_Cl - PV;
                    Y = S0;
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
                    cash_return = MV + Tk_Cl - PV;
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

    // State update on clock
    always @(posedge Reset, posedge Clock) begin
        if (Reset) begin
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
