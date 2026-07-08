# 2026 年 7 月 3 日
## 今日進度：
### 影片：看 TT 小教室第 1~5 課。
### 刷題：完成 HDLBits 的 "basics" 到 "8-bit wide shift register of length 3(Three module)"。

## 遇到的困難與解決方案：
### 問題：在寫always、case語法撰寫不完整，導致編譯錯誤。
### 解法：於程式後面加上end、endcase，成功編譯。
```verilog
always @(*) begin
    case()

    endcase
end
```

## 關鍵知識/詞彙：
  * 數位電路組成（logic gate、memory、flip-flop、wire、bus、IO）
  * 晶圓廠能製作的layoyt檔（GDS）
  * Tape out；RTL（Register-Transfer Level）較 Gate-level-netlist 高階
  * wire：連線（搭配 assign 語法）-> combinational logic
  * reg（register）：暫存器（搭配 always 語法但不一定合成Flip-Flop）
  * 硬體思維：同時持續處理（平行處理），程式碼先後順序不影響結果
  * Multi-driven（重複驅動）：一條線被2個以上訊號驅動
  * assign語法（持續賦值，不論順序）：用單等號＂＝＂（左邊需是 wire 形式，右邊可 wire 可 reg）。如果左位寬小於右邊，缺少的高位數會消失。
  * always語法（條件賦值）：
    * always@(posedge clk)begin -> 循序邏輯（sequential logic），賦值用＂＜＝＂（non-blocking）
    * always@(*)begin -> 組合邏輯（combinational logic），賦值用＂＝＂(blocking)，可創造出循序/組合邏輯。
    * always裡面的變數須是reg形式。先給初始值 or 條件寫滿避免"Latch"
  * 正反器（Flip-Flop）：又稱暫存器，同步數位電路最重要組成元件。與clock同步（上沿 0->1 positive edge, 下沿 1->0 negative edge）。邏輯深度決定電路速度
  * 分清楚reset訊號跟clock是同步/非同步
  * 同步reset（reset隨clock動作）：例always@(posedge clk)begin
  * 非同步reset（reset一來就動作）：例always@(posedge clock or negedge clk rst_n)begin
  * 震盪器（Oscillator）：always #<一半的週期時間> clk=~clk（通常用在Testbench產生clk，一般數位電路不會這樣寫）
---------------------------------------------
# 2026 年 7 月 4 日
## 今日進度：
### 影片：看 TT 小教室第 6~7 課。
### 刷題：複習 HDLBits 的 "basics" 到 "8-bit wide shift register of length 3(Three module)"。

## 遇到的困難與解決方案：
### 問題：在Connecting Signals to Module Ports的時候，搞錯"by position"與"by name"這兩種方法，by position與順序有關。
### 解法：複習兩種觀念，並搞懂兩者順序關聯，by position與順序有關，by name與順序無關。
```verilog
 //mod_a要求：module mod_a ( output, output, input, input, input, input );
 module top_module ( 
    input a, 
    input b, 
    input c,
    input d,
    output out1,
    output out2
);
    
    mod_a(out1, out2, a, b, c, d); //不可寫成mod_a(a, b, c, d, out1, out2);

endmodule
```

## 關鍵知識/詞彙：
### 位元運算元：
  
 | 符號 | 名稱 |
 | --- | --- |
 | ～ | Bitwise NOT |
 | ＆ | Bitwise AND |
 | ｜ | Bitwise OR |
 | ︿ | Bitwise XOR |

**每個位元獨立計算，IO位寬相同**

### 邏輯運算元：
  
 | 符號 | 名稱 |
 | --- | --- |
 | ！ | Logic NOT |
 | ＆＆ | Logic AND |
 | ｜｜ | Logic OR |

**判斷T/F，output為1bit**

### Reduction運算元：
  
 | 符號 | 名稱 |
 | --- | --- |
 | ＆ | Reduction AND |
 | ｜ | Reduction OR |
 | ︿ | Reduction XOR |

**將運算元右邊陣列中的每一位元做運算，output為1bit**
* EX. a = 4'b0110, &a = 0 & 1 & 1 & 0 = 1'b0
  
### 關係運算元：
  
 | 符號 | 名稱 |
 | --- | --- |
 | ＝＝ | 邏輯等於 |
 | ！＝ | 邏輯不等於 |
 | ＝＝＝ | 4態等於 |
 | ！＝＝ | 4態不等於 |

**比較結果皆為1bit，＝＝＝包含 Uknown X 和 Hign Z的比較（當文字比較），＝＝遇到X or Z結果都是 X**

### 條件運算元（？：）：
**選擇條件 ? 條件成立結果 : 條件不成立結果**
    
### signed (有正負號數) vs unsigned (無正負號數)
* 宣告 wire 或 reg 時，預設為**unsigned**值
* 如果變數需要正負號，要加上**signed**保留字
* 這樣該變數的最高位(**MSB**)就是正負號**Signed bit**， 0 代表正數， 1 代表負數
* **signed**變數需要補位時，會用**signed bit**補 (**signed extension**)
* 例如：
  * `wire signed [7:0] a;`
  * `wire signed [3:0] b;`
  * `a[7]`和`b[3]`就分別是`a`和`b`的正負號
  * `assign a = 8'b1000_0011;` $\rightarrow$ `a= -125 (2's complementary)`
  * `assign b = 4'b0010;` $\rightarrow$ `b= +2`

### 數學運算元

| 符號 | 名稱 | 說明 | 範例 |
| --- | --- | --- | --- |
| ＋ | 加法 | 兩數相加 | assign z0 = a + b; |
| － | 減法 | 兩數相減 | assign z1 = a - b; |
| ＊ | 乘法 | 兩數相乘 | assign z2 = a * b; |
| ／ | 除法 | 兩數相除 | assign z3 = a / b; |
| ＊＊ | 指數 | 底數的次方 | assign z4 = a ** b; |
| ％ | 餘數 | 算餘數 | assign z5 = a % b; |

**注意位寬會不會Overflow（溢位）**
**注意是有正負號數 (**signed**) 還是無正負號數 (**unsigned**) 的運算**


### 位移運算元

| 符號 | 名稱 | 說明 | 範例 |
| --- | --- | --- | --- |
| >> | 邏輯右移 | 將變數右移某一固定位數，Signed bit不補位 | assign z1 = a >> 3; |
| << | 邏輯左移 | 將變數左移某一固定位數，LSB位補0 | assign z2 = a << 3; |
| >>> | 數學右移 | 將變數右移某一固定位數，Signed bit會補位 | assign z3 = a >>> 3; |
| <<< | 數學左移 | 將變數左移某一固定位數，LSB位補0 | assign z4 = a <<< 3; |

**注意位移的數量必須是 **常數** 才能合成出正確的結果**
**注意位移後的 **位寬** ，尤其是左移時，不夠時會丟失位元**
**注意是 **signed** 或是 **unsigned** 的型態，>> 和 >>> 結果不同**

### 位移運算元應用

**把一個8 bits整數除以8後四捨五入**
* wire [7:0] a;
* wire [5:0] z1;
* assign z1 = (a >> 3) + a[2];

**把一個8 bits整數除以8後無條件進位**
* wire [7:0] a;
* wire [5:0] z2;
* assign z2 = (a >> 3) + |a[2:0];
  
**把一個8 bits整數除以8後無條件捨去**
* wire [7:0] a;
* wire [4:0] z3;
* assign z3 = (a >> 3);
---------------------------------------------
# 2026 年 7 月 5 日
## 今日進度：
### 影片：看 TT 小教室第 8~11 課。
### 刷題：完成 HDLBits 的 "Adder1" 到 "Adder2"。

## 遇到的困難與解決方案：
### 問題1：
* Adder1（用兩個16位元加法器模塊合成出一個32位元加法器）
* 應題目需求：32位的加法器不需要處理進位（假設為 0）或出位（忽略)
* **ins1進位端無空接或用1位元的wire對接導致編譯錯誤**
* 原程式碼：
  ```verilog
  module top_module(
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
    );

    wire k; //處理inst0進位
    add16 inst0(a[15:0], b[15:0], 1'b0, sum[15:0], k);
    add16 inst1(a[31:16], b[31:16], k, sum[31:16], sum); 
    endmodule
    ```
  
### 解法：
* **宣告一wire"count_useless"用來接最後丟棄的進位**
  ```verilog
  module top_module(
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
    );
    //module add16 ( input[15:0] a, input[15:0] b, input cin, output[15:0] sum, output cout );
    wire k, cout_useless;// count_useless用來接最後丟棄的進位
    add16 inst0(a[15:0], b[15:0], 1'b0, sum[15:0], k);
    add16 inst1(a[31:16], b[31:16], k, sum[31:16], cout_useless);

    endmodule
  ```
* **更好的寫法（Named Connection）**
  ```verilog
  module top_module(
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
    );
    wire k;

    // 低 16 位加法
    add16 inst0(
        .a(a[15:0]),
        .b(b[15:0]),
        .cin(1'b0),
        .sum(sum[15:0]),
        .cout(k)
    );
    
    // 高 16 位加法
    add16 inst1(
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(k),
        .sum(sum[31:16]),
        .cout() // 最高位進位如果不要，在具名寫法中直接留空即可
    );

    endmodule
  ```
  ### 問題2：
  * Adder2（Module fadd）
  * 不知為何需特別把module add1寫出來
  * 程式碼：
  ```verilog
  module top_module (
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
    );//
    
    wire k;
    wire cout_unused; // 用來接最後丟棄的進位

    // inst0 算出低 16 位，結果直接寫入 sum[15:0]
    add16 inst0(a[15:0], b[15:0], 1'b0, sum[15:0], k);
    
    // inst1 算出高 16 位，結果直接寫入 sum[31:16]
    add16 inst1(a[31:16], b[31:16], k, sum[31:16], cout_unused);

    endmodule

    module add1 ( input a, input b, input cin,   output sum, output cout );

    assign sum = (a ^ b) ^ cin;
    assign cout = a & b | a & cin | b & cin;// Full adder module here

    endmodule
  ```
  ### 解法：
  * **為補全最底層電路，將最底層寫的assign sum = a ^ b ^ cin;，透過中層的add16串 16次，最後再被top_module串了2次，最終在晶片上展開成一個巨大的 32 位元實體計算電路**
  * 題目用意在於：**模擬「底層 IP 不完整」的真實開發現狀、訓練「多層階層設計（Multi-level Hierarchy）」的思維**

## 關鍵知識/詞彙：
### if-else
* 由if開始，當判斷式成立時執行它的邏輯式，如果該判斷式不成立，則繼續看下一個else if的判斷式，直到某一個判斷式成立或是到達最後一個else。
* 有優先順序，按順序判斷，寫太深會影響電路速度
* 只能寫在always block裡，不論循序邏輯或是組合邏輯都可以（組合邏輯：blocking ；　循序邏輯：non-blocking）
* 組合邏輯裡的if-else要寫滿或是充當有預設值，不然會合出Latch

### clock gating
* Flip-Flop的clock觸發才會耗電，為節省電路功耗（省電），如果連續幾個cycle沒動作就把clock關掉，無觸發所以Q值不變
```verilog
always @(posedge clk) begin
    if (~rst_n) //reset為0（低態）觸發
        a <= 1'b0;
    else if (b)
        a <= 1'b1;
    else // 可以不寫最後這2行
        a <= a;
end
```

### case
* 只能寫在always block裡（循序、組合邏輯皆可）
* 當多選擇成立，則執行第一個成立的logic
* case、casez較常用，casez可用"?"表don't care
* 最後加default避免Latch，除非條件都寫滿
```verilog
case(狀態選擇)
    選擇1 : 邏輯式1:
    選擇2 : 邏輯式2:
    .
    .
    選擇n : 邏輯式n:
    default : 邏輯式x
endcase    
```

### for
* 只能寫在always block裡
* 迴圈條件須是固定值
* 每迴圈控制變量一定要能結束迴圈
* 常搭配array使用
```verilog
for (初始控制變量賦值; 迴圈條件; 每迴控制變量賦值) begin
    邏輯式;
end
```
* 陷阱
```verilog
module for_loop (
    input clk,
    input rst_n,
    output reg [7:0] z
);
integer i;
always @(posedge clk) begin
    if (~rst_n)
        z <= 1'b0;
    else begin
        for (i=0; i<8; i=i+1)
            z <= z + i; //不是把z值加上從1累加到7
    end
end
endmodule
```
### 遞延訊號
* 因為常需要根據pipline把需要的訊號延遲幾個cycle
* 例如某邊data path已經把值算出來，但另一邊卻要晚幾個cycle才準備好，所以可把需要的訊號延遲幾個cycle
```verilog
module pipe (
    input clk,
    input rst_n,
    input [7:0] a,
    output reg [7:0] z[8]
);
integer i;
always @(posedge clk) begin
    if (~rst_n) begin
        for (i=0; i<8; i=i+1)
            z[i] <= 1'b0;
    end
    else begin
        z[0] <= a;
        for (i=1; i<8; i=i+1)
            z[i] <= z[i-1]; //每個元素都會拿到前一個cycle值
    end
end
endmodule
```

### generate
* 把電路依據控制變量產生"generate"多次（用寫程式的方式，幫你自動大量複製硬體電路、線路或模組）
* 搭配for迴圈使用，但控制變量需用"genvar"宣告
* 可產生組合、循序邏輯、module的實例化instantiate
```verilog
genvar geni;
generate
    for (初始控制變量賦值; 迴圈條件; 每迴控制變量賦值) begin
        要generate的邏輯式或是instantiate的module
    end
endgenerate
```
* 範例（批量實例化模組 (Module Instantiation)）：用 generate for 串聯 4 個全加器做成 4-bit 加法器：
```verilog
genvar i;
generate
    for (i = 0; i < 4; i = i + 1) begin: adder_block
        // 自動複製 4 個 full_adder 模組，並把引腳與索引值 i 綁定
        full_adder fa_inst (
            .a(a[i]),
            .b(b[i]),
            .cin(carry[i]),
            .sum(sum[i]),
            .cout(carry[i+1])
        );
    end
endgenerate
```

### 函數（function）
* 用來將組合邏輯打包起來，方便重複使用
* 只能寫在module裡，且只在該module裡有效
* 可以寫在module的任意地方，但建議寫在最後面
* 只能寫組合邏輯
* 都是用 blocking（＝）
* 至少一個輸入變量（input）
* 只能有一個返回值，沒有輸出
* function可以調用其他function。
```verilog
function [bitwidth-1:0] 返回變數; // 「返回變數」即為函數名
    input 宣告;
    其他變數宣告;
    begin
        邏輯式;
    end
endfunction
```
### 函數調用
* 需賦值給一個變數
* 可賦值給wire、reg、output
* 可在assign右邊，也可在always block裡的=、<=右邊
```verilog
assign Z = 函數名(input1, input2, ...);

always @(*)
    Z = 函數名(input1, input2, ...);

always @(posedge clk)
    Z <= 函數名(input1, input2, ...);
```
* EX.信號的前後位元反轉
```verilog
module test_func (
    input [7:0] a,
    output [7:0] z
);

assign z = revert(a);

function [7:0] revert;
    input [7:0] data_in;
    integer i;
    begin
        for(i=0; i<8; i=i+1) begin
            revert[7-i] = data_in[i];
        end
    end
endfunction

endmodule
```
* **有時候在互傳數據時，習慣有的會先傳高位，有的先傳低位**

### define
* 通常被用來定義一些常數或是程式碼的開關
* 可以橫跨所有 modules 和 Hierarchy
* 使用時於名稱前面加一撇（註：即反單引號 `）

| 特性 |  `define` (全球巨集) | `parameter` (局部參數) |
| --- | --- | --- |
| **語法關鍵字** | 開頭帶有反單引號，如 ``define DATA_WIDTH 8` | 正常宣告，如 `parameter DATA_WIDTH = 8;` |
| **作用範圍** | **全域（Global）**。只要在編譯順序中被讀取，其後所有的 `.v` 檔案、所有 Module 都能直接使用。 | **區域（Local）**。只在宣告它的該個 `module` 內部有效。 |
| **使用方式** | 呼叫時前面一定要加一撇： |  |

* EX
```verilog
`define A_BW 8
`define B_BW 4
`define Z_BW (`B_BW+`A_BW)

module test_define_1 (
    input [`A_BW-1:0] a,
    input [`B_BW-1:0] b,
    output [`Z_BW-1:0] z
);
assign z = a + b + `Z_BW*2;
endmodule
```
 
### parameter
* 通常被用來定義一些常數或是 FSM state 的名稱
* 只在該module內有效，但可以在Hierarchy之間傳遞
* 使用時直接用名稱即可
* parameter必須有值，可以有算式，會計算好再使用。
* 語法1
```verilog
module m_name (input/output宣告);

parameter 名稱1 = 值1;
parameter 名稱2 = 值2;

endmodule
```
* 語法2
```verilog
module m_name #(
    parameter 名称1 = 值1,
    parameter 名称2 = 值2
)(
    input/output宣告
);
```
* 使用一（module內）：名稱
* 使用二（傳值）：
```verilog
#(.名稱1(新值1), .名稱2(新值2))
instant_name(IO連線)
```
* EX
```verilog
module test_parameter_0
#(
    parameter A_BW = 8,
    parameter B_BW = 4,
    parameter Z_BW = B_BW + A_BW
) (
    input [A_BW-1:0] a,
    input [B_BW-1:0] b,
    output [Z_BW-1:0] z
);

assign z = a + b + Z_BW*2;

endmodule

module test_top (
    input [6:0] in1,
    input [2:0] in2,
    output [9:0] out1
);

test_paramter_0 #(.A_BW(7), .B_BW(3)) //將A、B的值改變，驗證可在Hierarchy之間傳遞
 u_test_para (.a(in1), .b(in2), .z(out1));

endmodule
```
* **state machine建議都使用parameter來寫，可讀性較高**

### define 與 parameter 有什麼不同？
| 特性 | define (全球巨集) | parameter (局部參數) |
| --- | --- | --- |
| **語法關鍵字** | 開頭帶有反單引號，如 define DATA_WIDTH 8 | 正常宣告，如 parameter DATA_WIDTH = 8; |
| **作用範圍** | **全域（Global）**。只要在編譯順序中被讀取，其後所有的 .v 檔案、所有 Module 都能直接使用。 | **區域（Local）**。只在宣告它的該個 module 內部有效。 |
| **使用方式** | 呼叫時前面一定要加一撇 | |
---------------------------------------------
# 2026 年 7 月 6 日
## 今日進度：
### 刷題：完成 HDLBits 的 "carry-slect Adder" 到 "a priority encoder for 8-bit inputs"。

## 遇到的困難與解決方案：
### 問題：
* carry-slect Adder（選擇式加法器）
* **使用的位元範圍q0[31:16]、q1[31:16]超出了它原本宣告的[15:0]範圍，位元寬度（或範圍）越界錯誤導致編譯錯誤。**
* 原程式碼：
  ```verilog
  module top_module(
    input [31:0] a,
    input [31:0] b,
    output [31:0] sum
    );
    //module add16 ( input[15:0] a, input[15:0] b, input cin, output[15:0] sum, output cout );
    
    wire k;
    wire [15:0] q0,q1;
    add16 inst0(
        .a(a[15:0]),
        .b(b[15:0]),
        .cin(1'b0),
        .cout(k),
        .sum(sum[15:0])
    );
    
    add16 inst1(
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(1'b0),
        .cout(),
        .sum(q0[31:16]) //位元寬度（或範圍）越界錯誤
    );
    
    add16 inst2(
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(1'b1),
        .cout(),
        .sum(q1[31:16]) //位元寬度（或範圍）越界錯誤
    );
    
    always@(*)begin
        case(k)
            1'b0 : sum[31:16] = q0;
            1'b1 : sum[31:16] = q1;
        endcase
    end

    endmodule
    ```
  
### 解法：
* **修正q0、q1在inst1、inst2中的位元寬度**
  ```verilog
      add16 inst1(
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(1'b0),
        .cout(),
        .sum(q0[15:0]) //[31:16]修正為[15:0]
    );
    
    add16 inst2(
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(1'b1),
        .cout(),
        .sum(q1[15:0]) //[31:16]修正為[15:0]
    );
  ```
## 關鍵知識/詞彙：
### 優先編碼器
* 一種組合邏輯電路。當輸入一個多位元的向量（Vector）時，如果有多個位元同時為1，它會根據內定的優先權（通常是「最高位元優先」或「最低位元優先」），只輸出那第一個出現的1的二進位位置。
* 程式碼範例（4-bit priority encoder）：
  ```verilog
  // synthesis verilog_input_version verilog_2001
    module top_module (
    input [3:0] in,
    output reg [1:0] pos  );
    
    always @(*) begin
        if(in[0]) //當最低有效為為1，則輸出位置在q0,ex0001
            pos = 0;
        else if(in[1]) //ex.0010、0011（q0：don't care，有效位數較低）
            pos = 1;
        else if(in[2])
            pos = 2;
        else if(in[3])
            pos = 3;
        else
            pos = 0;
    end

    endmodule
  ```
  * 程式碼範例（a priority encoder for 8-bit inputs）：
    ```verilog
    // synthesis verilog_input_version verilog_2001
    module top_module (
    input [7:0] in,
    output reg [2:0] pos );
    
    always@(*)begin
        casez(in[7:0])
          8'bzzzzzzz1 : pos = 0;
          8'bzzzzzz1z : pos = 1;
          8'bzzzzz1zz : pos = 2;
          8'bzzzz1zzz : pos = 3;
          8'bzzz1zzzz : pos = 4;
          8'bzz1zzzzz : pos = 5;
          8'bz1zzzzzz : pos = 6;
          8'b1zzzzzzz : pos = 7;
          default : pos = 0;
        endcase
    end

    endmodule
    ```
---------------------------------------------
# 2026 年 7 月 7 日
## 今日進度：
### 影片：清大OCW王俊堯教授數位邏輯設計第 8B~8E 講。
### 資料：複習7/3 - 7/6進度。
### 刷題：複習7/3 - 7/6進度、完成 HDLBits 的 "Always nolatches" 到 "Create a 100-bit binary ripple-carry adder"。

## 遇到的困難與解決方案：
### 問題：
* Create a 100-bit binary ripple-carry adder
* **將module寫在module中、迴圈內呼叫的模組名稱寫錯導致編譯錯誤**
* 原程式碼
  ```verilog
  module top_module( 
    input [99:0] a, b,
    input cin,
    output [99:0] cout,
    output [99:0] sum 
    );
    
    add1 inst0(
        .a(a[0]),
        .b(b[0]),
        .cin(cin),
        .sum(sum[0]),
        .cout(cout[0])
    );
    
    genvar i;
	generate
        for (i=1 ; i<100 ; i++) begin : full_adder100
            add100 addi(　//呼叫名稱錯誤
                .a(a[i]),
                .b(b[i]),
                .cin(cout[i-1]),
                .sum(sum[i]),
                .cout(cout[i])
            );
    	end
	endgenerate

    module add1( //module需是獨立的
        input a, b,
        input cin,
        output cout,
        output sum
    );
        assign sum = a ^ b ^ cin;
    	assign cout = a & b | b & cin | a & cin;
        
     endmodule
    
    endmodule
  ```
  
### 解法：
* **每個模組都必須是獨立的個體**，把module add1的整段程式碼，移到top_module的endmodule外
* 把迴圈內部的 add100 改成 add1（最下面定義的基礎 1-bit 全加器名字叫做 add1）
* 修正後程式碼
```verilog
module top_module( 
    input [99:0] a, b,
    input cin,
    output [99:0] cout,
    output [99:0] sum 
);
    
    add1 inst0( // 第 0 級全加器：手動連接初始的 cin
        .a(a[0]),
        .b(b[0]),
        .cin(cin),
        .sum(sum[0]),
        .cout(cout[0])
    );
    
    genvar i;
	generate
        for (i=1 ; i<100 ; i++) begin : full_adder100 // 第 1 到 99 級全加器：利用 generate 迴圈自動串聯
            add1 addi(
                .a(a[i]),
                .b(b[i]),
                .cin(cout[i-1]),
                .sum(sum[i]),
                .cout(cout[i])
            );
    	end
	endgenerate

endmodule

module add1(
        input a, b,
        input cin,
        output cout,
        output sum
    );
        assign sum = a ^ b ^ cin;
    	assign cout = a & b | b & cin | a & cin;
        
 endmodule
```
## 關鍵知識/詞彙：
### Active Low
* 數位電路與晶片設計中，設計師確實非常偏愛使用**低態觸發 / 低電平有效（Active Low）**，由**硬體製程、電路特性以及抗干擾能力**等底層因素共同決定。
* 整理：

| 觸發方式 | 晶片負擔 | 抗干擾度 | 斷線安全（保護類訊號） | 多元件共享線路 |
| --- | --- | --- | --- | --- |
| **低態觸發（Active Low）** | **輕**（灌電流能力強） | **高**（對地低阻抗） | **高**（電壓崩潰時自動觸發保護） | **極易**（直接線與連結） |
| **高態觸發（Active High）** | **重**（拉電流能力弱） | **較低** | 較低 | 困難（需額外加邏輯閘） |

### Hazard
* 因為**硬體元件的物理延遲或設計缺陷**，導致電路在某個瞬間產生錯誤輸出（毛邊），或是讓處理器讀取到錯誤資料
* 分類
    * Static Hazard：輸入改變後，原本預期要維持穩定的輸出，卻在中間短暫跳變。
        * Static 1-Hazard：預期維持 1，中間卻掉了下去（1 → 0 → 1）。
        * Static 0-Hazard：預期維持 0，中間卻彈了上來（0 → 1 → 0）。
    * Dynamic Hazard：預期要從 0 變 1（或 1 變 0），但因為多條路徑延遲，輸出沒有一次到位，而是跳動了多次（0 → 1 → 0 → 1）。
* 防範方式
    * 加入冗餘項 (Redundant Terms / Hazard Cover)：利用卡諾圖（Karnaugh Map）圈選相鄰群組時，在兩個群組的交界處額外多圈一個「冗餘乘積項」（卡諾圖上的圈圈重疊）。多出來的邏輯閘能確保當輸入訊號在兩組之間切換時，輸出不會因為延遲而掉下去。

    * 改用同步時序電路 (Design Synchronous Logic)：現代 IC 設計最核心的解法。不要直接使用組合邏輯的輸出作為下一個電路的觸發訊號。在組合邏輯後面接一個正反器（Flip-Flop），並由全域時脈（Clock）控制。因為**毛邊只會發生在時脈週期的中間**，只要我們確保在時脈邊緣（Setup Time / Hold Time）來臨時訊號已經穩定，正反器就不會鎖存到毛邊。
---------------------------------------------
# 2026 年 7 月 8 日
## 今日進度：
### 資料：複習7/3 - 7/7進度。
### 刷題：複習7/3 - 7/7進度、完成 HDLBits 的 "100-digit BCD ripple-carry adder（100 位的 BCD 級聯加法器）"。

## 遇到的困難與解決方案：
### 問題：
* Create a 100-digit BCD ripple-carry adder
* **Module埠宣告中，cout被定義成一個"只有 1 位元的單一導線"，沒有維度可以使用中括號[]去指定索引**，所以編譯錯誤
* 原程式碼
  ```verilog
	module top_module( 
    input [399:0] a, b,
    input cin,
    output cout,
    output [399:0] sum );
      
    bcd_fadd inst0(
        .a(a[3:0]),
        .b(b[3:0]),
        .cin(cin),
        .sum(sum[3:0]),
        .cout(cout[0]) //沒辦法指定索引值
    );
    
    genvar i;
    generate
        for(i=1; i<100; i++)begin : digst100
            bcd_fadd insti(
                .a(a[(4*i)+3:(4*i)]),
                .b(b[(4*i)+3:(4*i)]),
                .cin(cout[i-1]),
                .sum(sum[(4*i)+3:(4*i)]),
                .cout(cout[i]) //沒辦法指定索引值
            );
        end
    endgenerate
       
	endmodule
  ```
  * 第一次修正後程式碼（加100條內部進位線cout -> cin）
  * **內部串聯的最後一個進位訊號cout_temp[99]無傳送給輸出埠cout**再次編譯錯誤
    ```verilog
    module top_module( 
    input [399:0] a, b,
    input cin,
    output cout,
    output [399:0] sum );
    
    wire [99:0]cout_temp; //加100條內部進位線cout_temp[i] -> cin[i+1]
    
    bcd_fadd inst0(
        .a(a[3:0]),
        .b(b[3:0]),
        .cin(cin),
        .sum(sum[3:0]),
        .cout(cout_temp[0])
    );
    
    genvar i;
    generate
        for(i=1; i<100; i++)begin : digst100
            bcd_fadd insti(
                .a(a[(4*i)+3:(4*i)]),
                .b(b[(4*i)+3:(4*i)]),
                .cin(cout_temp[i-1]),
                .sum(sum[(4*i)+3:(4*i)]),
                .cout(cout_temp[i])
            );
        end
    endgenerate

	endmodule
    ```  
### 解法：
* 修正後程式碼（加100條內部進位線；cout_temp[99]傳送給輸出埠cout）
```verilog
  module top_module( 
    input [399:0] a, b,
    input cin,
    output cout,
    output [399:0] sum );
    
    wire [99:0]cout_temp; //加100條內部進位線cout_temp[i] -> cin[i+1]
    
    bcd_fadd inst0(
        .a(a[3:0]),
        .b(b[3:0]),
        .cin(cin),
        .sum(sum[3:0]),
        .cout(cout_temp[0])
    );
    
    genvar i;
    generate
        for(i=1; i<100; i++)begin : digst100
            bcd_fadd insti(
                .a(a[(4*i)+3:(4*i)]),
                .b(b[(4*i)+3:(4*i)]),
                .cin(cout_temp[i-1]),
                .sum(sum[(4*i)+3:(4*i)]),
                .cout(cout_temp[i])
            );
        end
    endgenerate
    
    assign cout = cout_temp[99]; //cout_temp[99]傳送給輸出埠cout

	endmodule
```
## 關鍵知識/詞彙：
### Create a 100-digit BCD ripple-carry adder
* 更好的撰寫方式（減少編譯器的剖析樹（Parse Tree）深度、編譯時間，因為消除了硬體算術公式）
  ```verilog
  module top_module( 
    input [399:0] a, b,
    input cin,
    output cout,
    output [399:0] sum 
	);
    
    wire [99:0] cout_temp; // 100 級的內部進位線
    
    //直接平行宣告 100 個 bcd_fadd 實例！
    // 語法結構：模組名 實例名 [範圍] ( 埠接線 );
    bcd_fadd inst [99:0] (
        .a(a),                // 自動將 400-bit 對應到 100 個 4-bit 輸入
        .b(b),                // 自動將 400-bit 對應到 100 個 4-bit 輸入
        .cin({cout_temp[98:0], cin}), // 關鍵：把前 99 個進位與初始 cin 串起來當作 100 個 cin
        .sum(sum),            // 自動將 400-bit 對應到 100 個 4-bit 輸出
        .cout(cout_temp)      // 100 個進位輸出直接接給 cout_temp
    );
    
    assign cout = cout_temp[99]; // 將最後一級的進位送給頂層

	endmodule
  ```
* bcd_fadd模組程式碼
  ```verilog
  module bcd_fadd (
    input [3:0] a,     // 4-bit BCD 數字 (0~9)
    input [3:0] b,     // 4-bit BCD 數字 (0~9)
    input cin,         
    output cout,       
    output [3:0] sum   
	);

    reg [4:0] sum_temp; //設計5-bit寬度是為了捕捉最高進位，防止4-bit溢位 (最大值：9 + 9 + 1 = 19)

    always @(*) begin
        sum_temp = a + b + cin;
        
        //判斷有沒有超過十進位的9，超過需加6
        if (sum_temp > 5'd9) begin
            sum  = sum_temp + 5'd6; // 超過9，加6
            cout = 1'b1;            // 產生 BCD 進位
        end else begin
            sum  = sum_temp[3:0];   // 沒超過 9，直接當作結果
            cout = 1'b0;            // 不進位
        end
    end

	endmodule
  ```
---------------------------------------------
