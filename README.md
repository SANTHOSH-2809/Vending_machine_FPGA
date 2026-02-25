# Vending-Machine-FSM
## ✨ Description
This project implements a Finite State Machine (FSM) for a vending machine using Verilog HDL. The vending machine accepts coins, tracks the total amount, dispenses products when the required amount is reached, and returns change if necessary..0

## 🔖 Project Attributes
- Language: Verilog HDL  
- Concept: Finite State Machine (FSM)  
- Application: Vending Machine (coin-based)  
- Tools Used: Xilinx Vivado (for simulation & synthesis)  
- Features: RTL design, Testbench, State Diagram, Simulation waveforms  
- Category: Digital Design / FPGA Project

## ⚡ Features

| Feature | Description |
|---------|-------------|
|  **Accepts Multiple Coin Types** | Supports ₹1 and ₹2 coins |
|  **Automatic Product Dispensing** | Dispenses product when ₹5 is reached |
|  **Change Return System** | Returns the change if ₹6 is reached  |
| **Error Handling (Invalid Coin)** | Handles invalid coin inputs gracefully |
|  **Simulation Testbench** | Includes Verilog testbench for simulation |
| **Vivado / HDL Implementation** | Fully compatible with Xilinx Vivado tools |

## 📜 Vending machine
   <pre>module vend(
    input clk,
    input reset, 
    input [1:0] coin,
    output reg done,
    output reg [3:0]product_dispense,
    output reg return_change
);
  parameter [2:0] IDLE = 3'b000, ONE = 3'b001, TWO = 3'b010, THREE = 3'b011;
  parameter [2:0] FOUR = 3'b100, FIVE = 3'b101, SIX = 3'b110;

  localparam COIN1 = 2'b01, COIN2 = 2'b10;

  reg [2:0] Pstate, Nxt;
  reg previous;
  always @(posedge clk or posedge reset) begin
    if (reset)
      Pstate <= IDLE;
    else begin
      Pstate <= Nxt;
    end
  end
  always @(*) begin
    Nxt = Pstate;
    case (Pstate)
      IDLE: begin
        if (coin == COIN1) Nxt = ONE;
        else if (coin == COIN2) Nxt = TWO;
        else Nxt= IDLE;
      end
      ONE: begin
        if (coin == COIN1) Nxt = TWO;
        else if (coin == COIN2) Nxt = THREE;
        else Nxt= ONE;
      end
      TWO: begin
        if (coin == COIN1) Nxt = THREE;
        else if (coin == COIN2) Nxt = FOUR;
        else Nxt=TWO;
      end
      THREE: begin
        if (coin == COIN1) Nxt = FOUR;
        else if (coin == COIN2) Nxt = FIVE;
        else Nxt= THREE;
      end
      FOUR: begin
        if (coin == COIN1) begin
          Nxt = FIVE;
          previous = 1'b0;
        end else if (coin == COIN2) begin
          Nxt = SIX;
          previous = 1'b1;
        end
        else Nxt = FOUR;
      end
      FIVE: Nxt = IDLE;
      SIX:  Nxt = IDLE;
    endcase
  end
  
  always @(posedge clk or posedge reset) begin
    if (reset) begin
      product_dispense <= 0;
      return_change <= 0;
      done<=0;
    end else begin
      case (Pstate)
        FIVE: begin
          product_dispense <= 1;
          if(product_dispense)
            done<=1;
          if(coin==COIN1)
            previous=1;
        end
        SIX: begin
          product_dispense <= 1;
          return_change <= 1;
          if(product_dispense)
            done<=1;
        end
        default: begin
          product_dispense <= 0;
          return_change <= 0;
          done<=0;
        end
      endcase
    end
  end

endmodule</pre>

## 💻 Vending Mavhine.tb
<pre>module tb_vend;
  reg clk, reset;
  reg [1:0] coin;
  wire product_dispense;
  wire return_change;
  vend u_1(
    .clk(clk),
    .reset(reset),
    .coin(coin),
    .product_dispense(product_dispense),
    .return_change(return_change)
  );
  initial begin
    clk = 0;
    forever #3 clk = ~clk;
  end
  task insert_coin(input [1:0] c);
    begin
      coin = c;
      #6;            
      coin = 2'b00; 
    end
  endtask
  initial begin
    $dumpfile("vend.vcd");
    $dumpvars(0, tb_vend);
    $monitor("time=%0t, clk=%b, coin=%b, reset=%b, product_dispense=%b, return_change=%b",
             $time, clk, coin, reset, product_dispense, return_change);

    coin = 2'b00;
    reset = 1'b1;
    #5 reset = 1'b0;

    insert_coin(2'b01);
    insert_coin(2'b01);
    insert_coin(2'b01); 
    insert_coin(2'b01); 
    insert_coin(2'b01); 
    insert_coin(2'b01); 
    insert_coin(2'b01); 
    insert_coin(2'b01); 
    insert_coin(2'b01); 
    insert_coin(2'b10); 
    insert_coin(2'b01);
    insert_coin(2'b01); 
    insert_coin(2'b01);
    insert_coin(2'b10); 
    insert_coin(2'b01); 
    insert_coin(2'b01); 
    insert_coin(2'b10); 
    insert_coin(2'b01); 
    insert_coin(2'b01); 
    insert_coin(2'b10); 
    insert_coin(2'b01); 
    insert_coin(2'b01);
    insert_coin(2'b10);
    insert_coin(2'b01);
    insert_coin(2'b01);
    insert_coin(2'b01); 
    insert_coin(2'b10); 
    insert_coin(2'b10); 
    insert_coin(2'b01); 
    insert_coin(2'b10); 
    insert_coin(2'b01); 
    insert_coin(2'b00); 
    insert_coin(2'b10); 
    insert_coin(2'b01);
    insert_coin(2'b10);
    insert_coin(2'b10);
    insert_coin(2'b10);
    insert_coin(2'b10); 
    insert_coin(2'b10); 
    

    #5;
    $finish;
  end
endmodule</pre>
## 📂 Files
[Constraints](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Constraints)

[Top Module](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Top%20Module)

[Clock Divider](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Clock%20Divider)

[Problem Statement](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Problem%20Statement)


## 🔍 Possible Operation
|     Coin Sequence     |      Output      |   Return Change   |
|-----------------------|------------------|-------------------|
| ₹1 → ₹1 → ₹1 → ₹1 → ₹1| Dispense product |No Change          |
| ₹2 → ₹1 → ₹1 → ₹1     | Dispense product |No Change          |
| ₹1 → ₹2 → ₹1 → ₹1     | Dispense product |No Change          |
| ₹1 → ₹1 → ₹2 → ₹1     | Dispense product |No Change          |
| ₹1 → ₹2 → ₹1 → ₹2     | Dispense product |No Change          |
| ₹1 → ₹1 → ₹1 → ₹1 → ₹2| Dispense peoduct |Return change of ₹1|        
| ₹2 → ₹2 → ₹2          | Dispense product |Return change of ₹1|

## 🔌 Pin Assignment

|Signal|Direction|Pin|
|------|---------|---|
|Coin 0|Input|F22|
|Coin 1|Input|G22|
|Clk   |Input|Y9|
|Reset |Input|H22|
|Product_Dispense|Output|U14|
|Return_Change|Output|U19|
|Done|Output|T22|

### 🎥 Demo Video
[Watch the Simulation Video](https://drive.google.com/file/d/1lRF_kGa88yEdpU8BUpAoGncY8VQYMPgV/view?usp=drive_link) 

## 📸 State Diagram
![State Diagram](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Images/State%20Diagram.png)

## ⏱️ Design Timing Summary
![Design Timing Summary](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Images/Design%20Timming%20Summary.jpg)

## 📈 Output Waveform
![Output Waveform](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Images/Output%20Waveform.jpg)

## 🧩 Schematic Diagram
![Schematic Diagram](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Images/Schematic%20diagram%20.jpg)
![Schematic Diagram](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Images/Schematic%20diagram%201.jpg)

## 📊 Summary
![Summary](https://github.com/Rahul-Sivesh/Vending-Machine-FSM/blob/main/Images/Summary.jpg)

## 👥 Contributors
[Santhosh V](https://github.com/SANTHOSH-2809), ECE, Bannari Amman Institute Of Technology [Linked IN](https://www.linkedin.com/in/santhosh-v-31142232b/)

[Rahul Sivesh S](https://github.com/Rahul-Sivesh), ECE, Bannari Amman Institute of Technology [Linked IN](https://www.linkedin.com/in/rahul-sivesh-a78ab6329/)


  
