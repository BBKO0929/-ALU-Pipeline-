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
### 數位電路組成
* logic gate、memory、flip-flop、wire、bus、IO

### 晶圓廠能製作（Tape out）的layoyt檔
* GDS

### RTL（Register-Transfer Level）較 Gate-level-netlist比較
* RTL（Register-Transfer Level）較 Gate-level-netlist 高階

### wire連線
* （搭配 assign 語法）-> combinational logic

### reg（register）
* 暫存器（搭配 always 語法但不一定合成Flip-Flop）

### 硬體思維
* 同時持續處理（平行處理），程式碼先後順序不影響結果
  
### Multi-driven（重複驅動）
* 一條線被2個以上訊號驅動
  
### assign語法（持續賦值，不論順序）
* 用單等號＂＝＂（左邊需是 wire 形式，右邊可 wire 可 reg）。如果左位寬小於右邊，缺少的高位數會消失。
  
### always語法（條件賦值，有優先權問題）：
* always@(posedge clk)begin -> 循序邏輯（sequential logic），賦值用＂＜＝＂（non-blocking）
* always@(*)begin -> 組合邏輯（combinational logic），賦值用＂＝＂(blocking)，可創造出循序/組合邏輯。
* always裡面的變數須是reg形式。先給初始值 or 條件寫滿避免"Latch"
  
### 正反器（Flip-Flop）
* 又稱暫存器，同步數位電路最重要組成元件。與clock同步（上沿 0->1 positive edge, 下沿 1->0 negative edge）。邏輯深度決定電路速度
* 分清楚reset訊號跟clock是同步/非同步
* 同步reset（reset隨clock動作）：例always@(posedge clk)begin
* 非同步reset（reset一來就動作）：例always@(posedge clk or negedge rst_n)begin //低電位非同步reset
  
### 震盪器（Oscillator）
* always #<一半的週期時間> clk=~clk（通常用在Testbench產生clk，一般數位電路不會這樣寫）
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
* assign z1 = (a >> 3) + a[2]; //a[2]是位移之前的bit

**把一個8 bits整數除以8後無條件進位**
* wire [7:0] a;
* wire [5:0] z2;
* assign z2 = (a >> 3) + |a[2:0]; // |a[2:0] 是位移之前的 a[2] | a[1] | a[0]
  
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
        .sum(q0[15:0]) //[31:16]修正為[15:0]，雖同為 16 bits，但宣告範圍僅在 [15:0]
    );
    
    add16 inst2(
        .a(a[31:16]),
        .b(b[31:16]),
        .cin(1'b1),
        .cout(),
        .sum(q1[15:0]) //[31:16]修正為[15:0]，雖同為 16 bits，但宣告範圍僅在 [15:0]
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
* **Module埠宣告中，cout被定義成一個"只有 1 位元的單一導線"，沒有維度可以使用中括號 [] 去指定索引**，所以編譯錯誤
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
    
    //直接平行宣告 100 個 bcd_fadd 實例
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
# 2026 年 7 月 9 日
## 今日進度：
### 影片：看財經村長-數位IC設計面試1。
### 刷題：完成 HDLBits - "Combinational Logic" 中的 "Basic gates"。

## 遇到的困難與解決方案：
### 問題：
* Ringer
* **用軟體思維來寫硬體**
* 原程式碼：
  ```verilog
  module top_module (
    input ring,
    input vibrate_mode,
    output ringer,       // Make sound
    output motor         // Vibrate
	);

    assign ringer = (ring == 1'b1 && vibrate_mode == 1'b0) ? 1'b1 : 1'b0; //響鈴條件：有來電 (ring) 且 沒有開啟震動模式 (not vibrate_mode)
    assign motor = (ring == 1'b1 && vibrate_mode == 1'b1) ? 1'b1 : 1'b0; //震動條件：有來電 (ring) 且 開啟了震動模式 (vibrate_mode)
    
	endmodule
  ```
  * Gatesv(數位訊號處理與向量切片（Vector Slicing）練習)
  * 題目要求out_different檢查自己與左邊鄰居是否不同，in[3]的左邊是in[0]，但程式碼未將in[3]、in[0]做比較而導致編譯錯誤（位元寬度不匹配）
  * 原程式碼
  	```verilog
   	module top_module( 
    input [3:0] in,
    output [2:0] out_both,
    output [3:1] out_any,
    output [3:0] out_different );
    
    assign out_both = in[3:1] & in[2:0]; //檢查自己與左邊鄰居是否皆為 1 (3-bit 輸出)
    assign out_any = in[3:1] | in[2:0]; //檢查自己與右邊鄰居是否任一為 1 (3-bit 輸出，注意左側宣告是 [3:1])
    assign out_different = in[3:1] ^ in[2:0]; //位元寬度不匹配

	endmodule
  	```
### 解法：
* Ringer（更好的寫法）
* **業界**更推崇的精簡寫法：**直接使用邏輯閘**，**直接寫邏輯運算子**，腦海中可以直接浮現出電路圖。
* 修改後程式碼
```verilog
module top_module (
    input ring,
    input vibrate_mode,
    output ringer,       
    output motor         
);

    assign ringer = ring & ~vibrate_mode; //ring 獨立出現，就代表 ring == 1'b1；~vibrate_mode，就代表 vibrate_mode == 1'b0。
    assign motor = ring & vibrate_mode; 
    
endmodule
```
* Gatesv(數位訊號處理與向量切片（Vector Slicing）練習)
* out_different前三個位元 [2:0]：正常跟左邊鄰居比較（不用環繞），最高位元 [3]：單獨拉出來跟 in[0] 做環繞比較。
* 修改後程式碼
  ```verilog
  module top_module( 
    input [3:0] in,
    output [2:0] out_both,
    output [3:1] out_any,
    output [3:0] out_different );
    
    assign out_both = in[3:1] & in[2:0];
    assign out_any = in[3:1] | in[2:0];
    assign out_different[2:0] = in[3:1] ^ in[2:0];
    assign out_different[3] = in[3] ^ in[0];

	endmodule
  ```

## 關鍵知識/詞彙：
### Latch、Flip-flop根本差異
* Latch（平緣觸發）
  * 當控制信號(clk)處於有效電平時，Latch會持續追蹤輸入端的變化，並將這些變化反映到輸出端。
    
* Flip-flop（邊緣觸發）
  * 只在時鐘信號的特定邊緣(上升沿或下降沿)捕捉輸入資料，並在該瞬間更新輸出。

### Latch的電路實現與HDL描述
```verilog
module d_latch (
     input   rst_n,
     input   en,
     input   d,
     output  q
);

// 1. (!rst_n) -> 低電平重設，輸出 0
// 2. (en)     -> 致能開啟，輸出 d (隨輸入變動)
// 3. : q      -> 致能關閉，維持原值 (鎖存狀態)

	assign q = (!rst_n) ? 0 : (en) ? d : q;

 endmodule
```

### Setup Time與Hold Time的定義與重要性
* Setup Time
  * Setup time(Tsu)是指在時鐘有效邊緣(例如上升沿)到來之前，資料輸入端(D)的信號必須保持穩定不變的最短時間。確保Flip-flop內部的主Latch能夠正確地採樣並鎖存輸入資料。
  * Setup time的長短取決於Flip-flop內部電路的速度特性。
  
* Hold Time
  * Hold time(Th)是指在時鐘有效邊緣(例如上升沿)到來之後，資料輸入端(D)的信號必須繼續保持穩定不變的最短時間。確保Flip-flop能夠完全穩定地鎖存資料，新資料不會過早到達而破壞正在被捕捉的資料。
  * 通常比setup time短得多
  * Hold violation(保持時間違規)發生在資料路徑延遲太短，新資料過早到達的情況。與時鐘週期無關，通常需要透過插入延遲(如buffer)來修復。

### clock skew（時鐘偏斜的影響與計算）
* 同一個時脈源發出的訊號，到達晶片內不同觸發器（Flip-Flop）的時間差。
  
* 為甚麼會產生
  * 距離不同：有的觸發器離時脈輸入端（Clock Source）很近，有的拉了幾毫米遠。
  * 電阻電容（RC Delay）：金屬佈線本身有電阻和電容，線越長，訊號傳得越慢。（電容充電時間）
  * 緩衝器（Buffer）數量不同：為了推動大量硬體，時脈線上會加很多 Buffer，這也會帶來延遲。

* 對電路影響
  * 導致 Setup Time（建立時間）違規 ── 晶片跑不快
    * 如果後級的觸發器比前級「晚」收到時脈，前級提早變更資料，後級可能來不及發出正確訊號。
  * 導致 Hold Time（保持時間）違規 ── 晶片直接報廢
    * 如果後級的觸發器比前級「早」收到時脈，前級剛吐出的新資料可能會在後級還沒鎖存舊資料前，就直接衝過去把舊資料洗掉
    
* 如何解決
  * 建立「時鐘樹」（Clock Tree Synthesis, CTS）
    * 不能像接延長線那樣一條線拉到底，必須像大樹的樹枝一樣，確保從樹幹（主時脈）到每個樹葉（觸發器）的路徑 長度、Buffer 數量和負載完全對稱。
  * Clock Gating

* 設計實踐
  * 在實際專案中，時鐘樹設計通常佔用顯著的佈線資源和功耗。良好的時鐘樹設計不僅要控制skew，還要考慮功耗、面積、可測試性等多個維度。
 
### setup time violation 修復策略 1 - 增加clk週期（降頻）
* 優點：實施簡易
* 缺點：降低系統整體工作頻率、效能，設計中往往是最終選擇的方案

### setup time violation 修復策略 2 - 插入流水線暫存器(Pipelining)
* 在長的組合邏輯路徑中間插入額外的Flip-Flop，將一個長路徑分割成多個短路徑，從而減少每段的 data path delay
* 優點：可以顯著提高最大工作頻率，是高性能設計的標準做法。可以讓多個資料同時在不同階段處理，提高吞吐量
* 缺點：增加了資料的延遲週期(latency)，從輸入到輸出需要更多時鐘週期，還會增加面積和功耗，並可能使控制邏輯變得更複雜。
* 設計考量：切割點需要仔細選擇（有沒有辦法算出正確的值），要在邏輯的自然邊界處切割，避免造成組合邏輯的不平衡。

### setup time violation 修復策略 3
### Buffer插入與邏輯複製
* 當critical path上某個節點的負載(fanout)過大，導致驅動能力不足、轉換時間變慢、net delay增加時，可以採用兩種技術：
  * Buffer Insertion (插入緩衝器)：在長線路或高fanout節點插入repeater buffer，分段驅動負載，減少整體延遲。（能夠提供額外驅動能力，還能降低線路的RC延遲效應）
  * Logic Replication (邏輯複製)：複製產生高fanout信號的邏輯gate，讓每個副本驅動部分負載，從而減少單一gate的負擔。
* 權衡考量：Buffer insertion會增加面積和功耗，而logic replication則會增加更多面積(因為複製了邏輯)。

### Cell Sizing／Gate Upsizing
* 當timing report顯示cell delay佔比較大(而非net delay)，表示gate本身的驅動能力不足。解決方法是將標準單元庫中的小尺寸cell替換為大尺寸cell
* 作用機制：更大的cell具有更寬的電晶體通道，能提供更大的驅動電流，加快輸出轉換速度，降低cell delay。
* 大尺寸cell會增加面積、輸入電容(可能影響前級timing)、和功耗。（針對critical path上delay最大的幾個cell進行選擇性upsizing即可）

### 更換Vt類型(Threshold Voltage Tuning)
* LVT (Low-Vt)：速度最快(延遲最小)，但漏電流最大，靜態功耗高
* SVT (Standard-Vt)：性能與功耗的折衷選擇
* HVT (High-Vt)：速度最慢，但漏電流最小，適合非關鍵路徑
* 代價與風險：
  * 漏電功耗大幅上升，影響待機功耗和總功耗預算
  * Cell變快可能使hold time更緊張，因為最小路徑延遲減小
  * 可能加劇IR drop問題和on-chip variation，在某些corner下反而更差
  * 影響yield，因為LVT cell對製程變異更敏感。
* 最佳實踐：採用multi-Vt設計策略：critical path用LVT，非關鍵路徑用HVT，大部分用SVT。

### setup time violation 修復策略 4
### 時鐘偏斜優化(Clock Skew Scheduling)
* 刻意調整 clock skew，可以在不改變電路結構的情況下改善 timing。對於 setup violation，可以引入 positive skew，讓 capture FF 的時鐘稍微延遲到達，給予資料更多傳播時間。

### 邏輯重構(Logic Restructuring)
* 透過重新組織組合邏輯的結構來減少延遲，例邏輯分解(factoring)、critical path 的優先運算等

### 實體設計優化(Physical Design)
* 將相關的 cell 放得更近、使用較低層的金屬層以減少電阻、避開擁擠區域、使用更寬的線等

### **綜合性的優化策略**
* 一般的優化流程：
  * 先做大架構層面的改動：如增加流水線、降頻(如果可行)、重新 partition 模組等
  * 再做RTL 層級的優化：如邏輯重構、減少組合邏輯深度、優化狀態機等
  * 最後在 P&R 階段做細部調整：包括 gate sizing、buffer insertion...
  * 迭代優化：時序優化是迭代過程。修復一處 violation 可能在別處產生新的問題

### critical path
* 晶片內所有邏輯路徑中，訊號傳遞速度最慢、延遲（Delay）最長的那一條路徑

### hold time violation 修復策略 1 - 增加資料路徑延遲
* Hold violation與setup violation本質上相反；setup是資料來得太慢，hold是資料來得太快或變化得太早
* Hold violation與時鐘週期無關（頻率降到很低，hold問題依然存在）
* hold violation通常被認為比setup violation更嚴重（降頻無法修）
* **增加資料路徑延遲**可讓新資料晚一點到達capture FF

### hold time violation 修復策略 2
### 插入延遲元件
* 插入Buffer：在資料路徑上插入一個或多個 buffer，增加 propagation delay
* 插入位置：通常在 launch FF 的輸出端或組合邏輯路徑的早期階段插入（同時影響所有由該 FF 驅動的路徑）
* 插入Delay Cell：標準單元庫通常提供專用的 delay cell（如 DELAY、DELLN 等），這些 cell 專門用於增加延遲而不改變邏輯功能（相比普通 buffer、delay cell 有更可預測的延遲特性）
* 注意事項：增加面積和功耗（尤其是動態功耗）。

### 調整時鐘偏斜
* 減小 clock skew，甚至引入 negative skew（讓 capture FF 的時鐘提前到達），可以給資料更多的保持時間。
* 注意事項：調整 skew 會同時影響 setup 和 hold timing，需要做好 clock tree 的 balance。

### 插入 Lock-up Latch
* 在路徑中插入一個低電平有效的 Latch。在時鐘的高電平期間，latch 處於保持狀態，輸出不變；在低電平期間，latch 透明，資料通過。
* 注意事項：Lock-up latch 會增加 data latency（半個週期），並增加面積和功耗。

### Hold Fix的最佳實踐
* 實際設計流程中，hold violation的修復通常在P&R的後期階段進行，因為：
  * Hold timing對實際的placement和routing非常敏感,只有在physical design確定後,才能準確評估hold violations
  * 現代P&R工具通常有自動的hold fixing功能,會在post-CTS或post-route階段自動插入所需的buffer/delay cell
  * Hold fix通常是最後的收尾工作，在setup timing基本達標後才進行大規模的hold fixing。
  * 設計者需要在設定P&R constraints時,給予工具足夠的buffer insertion彈性，並在chip finishing階段仔細檢查hold fix的結果，確保沒有過度修復(over-fixing)或遺漏關鍵路徑。
---------------------------------------------
# 2026 年 7 月 10 日
## 今日進度：
### 資料：複習7/3 - 7/9內容。
### 刷題：複習 HDLBits 的 "Modules" 到 "Basic Gates"；完成 HDLBits - Arithmetic circuits 的 half adder 到 signed addition overflow。

## 遇到的困難與解決方案：
### 問題：誤解溢位判斷。
### 解法：
### 溢位只會發生在「這兩種情況」
* 正數 ＋ 正數 ＝ 變成負數（正溢位）
* 負數 ＋ 負數 ＝ 變成正數（負溢位）

### 溢位
* 專指「有號數（Signed Number）」在進行加減法運算時，因為答案太大或太小，導致 8-bit 的空間裝不下，進而使「符號位元（Sign bit）」被錯誤篡改的硬體災難。

### 今日例題 - signed addition overflow
```verilog
module top_module (
    input [7:0] a,
    input [7:0] b,
    output [7:0] s,
    output overflow
); //

    assign s = a + b;// 兩數相加
    assign overflow = ~(a[7] ^ b[7]) & (a[7] ^ s[7]);// 判斷溢位：最高位元同為1 or 0且與最高輸出位元數字相反

endmodule
```

## 關鍵知識/詞彙：
### Variable index　變數索引（為什麼「變數索引」在硬體裡是巨大的 MUX？）
* 在寫 C++ 或 Python 時，data[index] 只是叫 CPU 去記憶體某個地址「看一眼」，記憶體很大、index 再大也沒差。
* 晶片不是軟體，晶片是「用金屬線焊死」的電路板！。如果電路是 out = data[index]，這代表硬體必須做到：「不論 index 傳進來是多少，out 都要能拿到對應的資料。」（不可以有空的資料）
* 當 data 的範圍很大時，晶片的速度（時脈頻率）會被拖垮！

### Variable part-select 變數局部選擇（變數動態切片 +: 與 -:）
* 在硬體設計中，合成器有一個死命令：「拉出來的總線（Bus），在晶片做出來時，有幾根銅線必須是確定的！」
* 假設寫 out = data[index : index+3]：
  * 冒號左右兩邊都有變數（index 和 index+3），在數學解析上會判定「這個範圍的寬度可能隨時在變」，導致編譯錯誤
* 正確寫法（焊死寬度，只動起點）
  * data[起點 +: 寬度] or data[起點 -: 寬度]
  * "+"代表從起點往高位元數 4 個；"-"代表從起點往低位元數 4 個
* 今日範例程式 256-to-1 multiplexer
  ```verilog
  module top_module( 
    input [1023:0] in,
    input [7:0] sel,
    output [3:0] out );
    
    assign out = in[sel*4 +: 4]; //選出 1024bit 資料中，某位置的 4bit 資料

	endmodule
  ```      
---------------------------------------------
# 2026 年 7 月 11 日
## 今日進度：
### 資料：複習7/3 - 7/10內容。
### 刷題：複習 HDLBits 的 "Modules" 到 "multiplexers"；完成 HDLBits - Arithmetic circuits。

## 遇到的困難與解決方案：
### 問題：create a 4-digit BCD ripple-carry adder
* 在做賦值後出現寬度截斷警告（Truncation Warning）。
* 原程式碼：
  ```verilog
  module top_module ( 
    input [15:0] a, b,
    input cin,
    output cout,
    output [15:0] sum );
    
    wire [3:0]k;
    bcd_fadd inst0(
        .a(a[3:0]),
        .b(b[3:0]),
        .cin(cin),
        .sum(sum[3:0]),
        .cout(k[0])
    );
    
    genvar i;
    generate
        for(i=1; i<4; i++)begin : bcd_adder16
            bcd_fadd insti(
                .a(a[4*i+3 : 4*i]), //等號右邊算出來的結果是一個 32-bit 整數，但等號左邊的接收目標寬度卻只有 4-bit
                .b(b[4*i+3 : 4*i]), //等號右邊算出來的結果是一個 32-bit 整數，但等號左邊的接收目標寬度卻只有 4-bit
                .cin(k[i-1]),
                .sum(sum[4*i+3 : 4*i]),//等號右邊算出來的結果是一個 32-bit 整數，但等號左邊的接收目標寬度卻只有 4-bit
                .cout(k[i])
            );
        end
    endgenerate
    
    assign cout = k[3];

	endmodule
  ```
  * 編譯器強會行把高位元的 28 個 bit 全部丟棄，只留下最低的 4 個 bit。
### 解法：
### 改用變數動態切片 +: 與 -: 撰寫，明確告訴編譯器「起點是 4*i，寬度死死就是 4」
* 修改後程式碼
  ```verilog
  module top_module ( 
    input [15:0] a, b,
    input cin,
    output cout,
    output [15:0] sum );
    
    wire [3:0]k;
    bcd_fadd inst0(
        .a(a[3:0]),
        .b(b[3:0]),
        .cin(cin),
        .sum(sum[3:0]),
        .cout(k[0])
    );
    
    genvar i;
    generate
        for(i=1; i<4; i++)begin : bcd_adder16
            bcd_fadd insti(
                .a(a[4*i +: 4]), //動態切片 +: 與 -: 撰寫
                .b(b[4*i +: 4]), //動態切片 +: 與 -: 撰寫
                .cin(k[i-1]),
                .sum(sum[4*i +: 4]), //動態切片 +: 與 -: 撰寫
                .cout(k[i])
            );
        end
    endgenerate
    
    assign cout = k[3];

	endmodule
  ```

## 關鍵知識/詞彙：
### 16-bit BCD_adder
* 雖編譯成功且執行結果正確，但在硬體描述語言中踩到了「一線多接（Multiple Drivers）」的語法地雷！
* 雖然在某些極端寬容的編譯器可以過，但在很多標準的 Linter（語法檢查器）或 HDLBits 的環境中，將最後一級同時接到內部陣列又透過 assign 指定的方式，容易導致陣列邊界混淆或多重驅動判定錯誤。
* 原程式碼：
  ```verilog
  module top_module ( 
    input [15:0] a, b,
    input cin,
    output cout,
    output [15:0] sum );
    
    wire [3:0]k;
    bcd_fadd inst0(
        .a(a[3:0]),
        .b(b[3:0]),
        .cin(cin),
        .sum(sum[3:0]),
        .cout(k[0])
    );
    
    genvar i;
    generate
        for(i=1; i<4; i++)begin : bcd_adder16
            bcd_fadd insti(
                .a(a[4*i +: 4]),
                .b(b[4*i +: 4]),
                .cin(k[i-1]),
                .sum(sum[4*i +: 4]),
                .cout(k[i]) //k[3] 被千位數加法器的 .cout(k[3]) 驅動（塞資料進去）。
            );
        end
    endgenerate
    
    assign cout = k[3]; //同時又宣告了 assign cout = k[3];

	endmodule
  ```
  
* 更好的撰寫方式
  ```verilog
  module top_module ( 
    input [15:0] a, b,
    input cin,
    output cout,
    output [15:0] sum 
	);
    
    // 宣告 5 根進位線（k[0] 到 k[4]）
    wire [4:0] k;
    
    // 把頭尾焊死接到頂層介面
    assign k[0] = cin;
    assign cout = k[4];
    
    genvar i;
    generate
        for(i=0; i<4; i++) begin : bcd_adder16
            bcd_fadd insti(
                .a(a[4*i+3 : 4*i]),
                .b(b[4*i+3 : 4*i]),
                .cin(k[i]),       // i=0 時就是 cin
                .sum(sum[4*i+3 : 4*i]),
                .cout(k[i+1])     // i=3 時輸出給 k[4]，就是 cout
            );
        end
    endgenerate

	endmodule
  ```
---------------------------------------------
# 2026 年 7 月 13 日
## 今日進度：
### 資料：複習7/3 - 7/11內容。
### 刷題：完成 HDLBits 的 Karnaugh Map to Circuit 到 DFF with byte enable。

## 遇到的困難與解決方案：
### 問題：
### using one 4-to-1 multiplexer and as many 2-to-1 multiplexers as required
* 如何優化並寫成更精簡程式碼
* 原程式碼：
  ```verilog
	module top_module (
    input c,
    input d,
    output [3:0] mux_in
	); 
    
    assign mux_in[0] = c ? 1'b1 : d ? 1'b1 : 0; //如果 c 是 1 輸出 1；否則如果 d 是 1 輸出 1；如果都不是就輸出 0。
    assign mux_in[1] = 1'b0; //直接接地（GND）
    assign mux_in[3] = (c & d) ? 1'b1 : 1'b0; //當 c 和 d 同時為 1 時輸出 1。
    assign mux_in[2] = (~d) ? 1'b1 : 1'b0; //如果 ~d 成立就輸出 1，否則輸出 0。
    
	endmodule
  ```

### DFF with byte enable
* if - else 造成了不必要的「優先權」與「資料遺失」
* 原程式碼：
  ```verilog
  module top_module (
    input clk,
    input resetn,
    input [1:0] byteena,
    input [15:0] d,
    output [15:0] q
	);
    
    always@(posedge clk)begin //DFF 正緣觸發
        if(~resetn)　//低態reset
            q <= 16'h0;
        else if(byteena[1])
            q[15:8] <= d[15:8]; //byteena[1] controls the upper byte
        else if(byteena[0])
            q[7:0] <= d[7:0]; //byteena[0] controls the lower byte 
        else
            q <= d;
    end

	endmodule
  ```
* 當 **byteena = 2'b11（兩個位元組都要寫入）時**：因為 if (byteena[1]) 成立了，硬體執行完 q[15:8] <= d[15:8] 之後，就會直接跳過後面的 else if (byteena[0])，結果導致低位元組（q[7:0]）完全沒有更新。
* 當 **byteena = 2'b00（兩個位元組都不寫入，維持原值）時**：硬體會一路走到最後的 else，執行 q <= d;。這意味著即使致能訊號是 0，輸入資料 d 還是被硬生生寫進去了，暫存器失去了「保留舊值」的功能。

### 解法：
### using one 4-to-1 multiplexer and as many 2-to-1 multiplexers as required
* 改寫in[0]、in[2]、in[3]程式碼
* 修改後程式碼
  ```verilog
	module top_module (
    input c,
    input d,
    output [3:0] mux_in
	);    
    
    assign mux_in[0] = c | d;  // OR 閘（c or d 任一為 1 就輸出 1）
    assign mux_in[1] = 1'b0;   // 接地
    assign mux_in[2] = ~d;     // NOT 閘（反相 d 輸出 1）
    assign mux_in[3] = c & d;  // AND 閘（當 c and d 為 1 就輸出 1）
    
	endmodule
  ```

### DFF with byte enable
* 在實體晶片中，高位元組（High Byte, [15:8]）和低位元組（Low Byte, [7:0]）的寫入控制是各自獨立、互不干涉的。
* 在重置之外，我們應該用兩個獨立的 if 條件句來分別控制它們，而當致能訊號為 0 時，什麼都不要寫（隱式保留原值）
* 修改後程式碼：
  ```verilog
    module top_module (
    input clk,
    input resetn,
    input [1:0] byteena,
    input [15:0] d,
    output [15:0] q 
	);
    
    
    always @(posedge clk) begin
        if (~resetn) begin
            q <= 16'h0;       
        end 
		else begin
		
            if (byteena[1]) begin
                q[15:8] <= d[15:8]; 
            end
            
            if (byteena[0]) begin
                q[7:0] <= d[7:0];   
            end
            
            // 如果 byteena[1] 或 byteena[0] 為 0，
            // 沒寫 else 代表硬體會自動維持 q 的上一個狀態（保留原值），這才是正確的暂存器行為！
        end
    end

	endmodule
  ```
* 另寫法：
  ```verilog
  always @(posedge clk) begin
    	if (~resetn) begin
        	q <= 16'h0;
    	end
  		else begin
        	q[15:8] <= byteena[1] ? d[15:8] : q[15:8]; // 1 就寫入新值，0 就維持原本的 q
        	q[7:0]  <= byteena[0] ? d[7:0]  : q[7:0];
    	end
	end
  ```
    

## 關鍵知識/詞彙：
### 再次分析位元運算子（Bitwise）」與「邏輯運算子（Logical）
* 當訊號只有 1-bit 時，0 就是假，1 就是真。這時候位元運算（~, &）跟邏輯運算（!, &&）在數學上的結果完全等價，合成器最後長出來的實體電路也是同一個邏輯閘。  
* 雖然怎麼寫都對，但通常硬體工程師在編寫組合邏輯（處理資料、訊號線）時，會優先使用位元運算子（~, &, |），因為這樣最直覺地對應到硬體邏輯閘。 
* 邏輯運算子（!, &&, ||）通常只會保留給條件判斷（例如 if (rst_n && valid) 這種用來控制狀態機或觸發條件的地方）。

### 高電位非同步reset（8 D flip-flops with active high asynchronous reset）
* 範例 - Dff8ar
  ```verilog
  module top_module (
    input clk,
    input areset,   // 高電位非同步reset
    input [7:0] d,
    output [7:0] q
	);
    //觸發條件： clk 的正邊緣，以及 areset 的正邊緣（因為是高電位有效）
    always@(posedge clk or posedge areset)begin
        if(areset)
            q <= 0;
        else
            q <= d;
    end

	endmodule
  ```
  
### 高電位同步reset
  * 範例 - Dff8r（8 D flip-flops with active high synchronous reset）
    ```verilog
    module top_module (
    input clk,
    input reset,            // 同步 reset
    input [7:0] d,
    output [7:0] q
	);
    
    always@(posedge clk)begin
        if(reset)
            q <= 0;
        else
            q <= d;
    end

	endmodule

    ```

### 使用 begin...end 的 4 大核心好處
1. 確保多個硬體動作「同生共死」
* 晶片設計中，常常要在某個條件成立時，同時改變多個訊號（例如：重置時，要把 q 清零，同時把 valid 訊號也清零）。
* 好處： 用 begin...end 包起來，這兩件事在硬體上才會同時受到同一個條件控制。

2. 徹底絕殺「隱形 Bug」
* 如果只有一行而沒加 begin...end，未來你在維護、修改程式碼時，隨手多補了一行指令，編譯器並不會報錯，但這行新指令會直接脫離控制，產生非常難抓的邏輯 Bug（通常要進模擬器看波形才會發現）。
* 好處： 從一開始就寫好 begin...end，未來要隨時增加、刪除程式碼都非常安全。

3. 程式碼架構層次分明（極速 Debug）
* 當程式碼變得非常龐大，有大量的 if-else 嵌套（Nest）時，良好縮排的 begin...end 會形成一塊一塊的「電路區域」。
* 好處： 大幅縮短 Debug 的時間。

4. 業界標準規範（專業度的體現）
* 在聯發科（MediaTek）或各大 IC 設計廠的 Coding Style Guide（程式碼規範原則） 中，為了防止上述的各種人類肉眼失誤，通常都是強迫一律加上 begin...end。
---------------------------------------------
# 2026 年 7 月 14 日
## 今日進度：
### 刷題：完成 HDLBits 的 D latch 到 Implement a JK flip-flop with only a D-type flip-flop and gates（Exams/ece241 2013 q7）。

## 遇到的困難與解決方案：
### 問題：
### Exams/ece241 2014 q4
* z 的輸出邏輯錯誤 (Timing Bug)： z 是一個純組合邏輯，它是直接接在暫存器輸出 Q 後面的 NOR 閘。
* 將 z 一併寫在循序邏輯電路中，造成 output z 資料獲取比實際慢一個 D 正反器的時脈週期
* 原程式碼：
  ```verilog
	module top_module (
    input clk,
    input x,
    output z
	);
    
    reg [2:0]q;
    
    always@(posedge clk)begin
        q[0] <= x ^ q[0];
        q[1] <= x & (~q[1]);
        q[2] <= x | (~q[2]);
        z <= ~(q[0] | q[1] | q[2]); //將 z 一併寫在循序邏輯電路中
    end

	endmodule
  ```
  * 硬體長相：因為把 z <= ... 寫在 always @(posedge clk) 裡面，編譯器會認定：「z 也是一個必須在時脈正邊緣更新的暫存器！」
  * 實際電路：q[0], q[1], q[2] 的訊號先拉到一個 NOR 閘。NOR 閘的輸出，會再接一個實體的 D 暫存器，最後這個暫存器的輸出端才是 z。
    * 所以訊號從 q 到 z 之間，多過了一次「時脈關卡」。

### 解法：
### Exams/ece241 2014 q4
* 將 z 移到 always 區塊外面，用 assign 賦值
* 修改後程式碼
  ```verilog
	module top_module (
    input clk,
    input x,
    output z
	);
    
    reg [2:0]q;
    
    always@(posedge clk)begin
        q[0] <= x ^ q[0];
        q[1] <= x & (~q[1]);
        q[2] <= x | (~q[2]);
    end
    assign z = ~(q[0] | q[1] | q[2]);

	endmodule
  ```
  * 硬體長相：因為 assign 是組合邏輯。
  * 實際電路：q[0], q[1], q[2] 的輸出線拉出來，直接接進一個實體的 NOR 閘，NOR 閘的輸出端直接就是 z 了。
    * 所以沒有任何額外的暫存器。只要 q 發生改變，訊號穿過 NOR 閘（僅有極微小的物理延遲），z 就會**立刻**跟著改變。

## 關鍵知識/詞彙：
### 再次分析時序、組合邏輯
### 時序邏輯（需要存狀態、記憶上一次的值）
* 用 always @(posedge clk) 搭配 reg 與 <=（non-blocking）。

### 組合邏輯（純運算、不需記憶，只想即時得到結果）
* 用 assign 搭配 wire 與 = （blocking）。
---------------------------------------------
# 2026 年 7 月 15 日
## 今日進度：
### 資料：複習7/3 - 7/14內容。
### 刷題：複習 HDLBits 的 "Modules" 到 "Implement a JK flip-flop with only a D-type flip-flop and gates（Exams/ece241 2013 q7）"；完成 HDLBits - sequential logic。

## 遇到的困難與解決方案：
### 問題：
### Edgedetect（正邊緣偵測）
* 不理解邊緣偵測的核心硬體思想，最初思想為當clk正緣發生且有資料(in)輸入(資料為真)則邊緣偵測(pedge)為真。

### Edgecapture（邊緣捕獲暫存器）
1. 問題一
	* 當 reset 訊號為高電位時，程式只會執行 if 裡面的 out <= 0。此時 else 裡面的 in_previous <= in 完全不會被執行！
	* 導致在 reset 期間，in_previous 保持在舊的值。一旦 reset 變回 0 的下一個瞬間，因為 in_previous 沒有跟著被重置或同步更新，會瞬間觸發一個錯誤的負邊緣訊號，導致輸出產生非預期的脈衝。
* 程式
```verilog
  	module top_module (
    input clk,
    input reset,
    input [31:0] in,
    output [31:0] out
	);
    
    reg [31:0]in_previous;
    
    always@(posedge clk)begin
        if(reset)begin
            out <= 0;
        end
        else begin
            out <= (~in) & in_previous;
            in_previous <= in;
        end
    end

	endmodule
```

2. 問題二
   * 第一次修正後，偵測後立刻歸零，但題目要求的是「捕獲並保持（鎖定）」，所以再次編譯錯誤
* 程式碼
  ```verilog
  module top_module (
    input clk,
    input reset,
    input [31:0] in,
    output [31:0] out 
	);
    
    reg [31:0] in_previous;
    
    always @(posedge clk) begin
        
        in_previous <= in; 
        
        if (reset) begin
            out <= 32'b0; 
        end else begin
            out <= (~in) & in_previous; //只有在發生負邊緣的那一個週期 out 會是 1，下一個週期如果 in 維持 0（沒有新的邊緣），out 就會立刻自動變回 0。
        end
    end

	endmodule
  ```

### 解法：
### 範例程式 - Edgedetect（正邊緣偵測）
* 題目需求：檢測當輸入信號從一個時鐘週期內的 0 變為下一個時鐘週期內的 1 。 應該在 0 變為 1 的時鐘週期之後，設置該位。
* 程式碼
  ```verilog
  module top_module (
    input clk,
    input [7:0] in,
    output [7:0] pedge
	);
    
    reg [7:0]in_previous; // 用來儲存「上一個週期」輸入值的暫存器
    
    always@(posedge clk)begin
        pedge <= in & (~in_previous); //今天的 pedge 等於：今天為 1 且 昨天為 0
        in_previous <= in; //將今天的輸入值存進去，變成昨天的狀態，更新狀態
    end

	endmodule
  ```

### 邊緣偵測的核心硬體思想 - 準備一個「昨天」的暫存器
* 硬體上偵測「變化」需拿「（現在）的數值」跟「（前一個週期）的數值」做對比
* 如題意：正邊緣偵測要求的是 - 只有在 in 從 0 變 1 的那一下 pedge 輸出 1，之後即使 in 繼續維持 1，pedge（輸出）也必須立刻降回 0
* 其他邊緣偵測：
  * 負邊緣偵測：只有在輸入 0 變 1 的那一下邊緣偵測輸出 1，之後即使輸入繼續維持 1，輸出也必須立刻降回 0
  * 雙邊緣偵測：在輸入 0 變 1 、 1 變 0 的那一下邊緣偵測輸出 1，之後即使輸入繼續維持 1，輸出也必須立刻降回 0

### Edgecapture（邊緣捕獲暫存器）
1. 修正一
   * 不管有沒有 reset，in_previous 每個週期都應該要把當前的 in 存下來，或者在 reset 時也將它清零/初始化。
* 程式碼
  ```verilog
  module top_module (
    input clk,
    input reset,
    input [31:0] in,
    output [31:0] out 
	);
    
    reg [31:0] in_previous;
    
    always @(posedge clk) begin
        //每個時脈週期都把當前的 in 存下來，避免 reset 撤銷時產生假的邊緣
        in_previous <= in; 
        
        if (reset) begin
            out <= 32'b0;
        end else begin
            out <= (~in) & in_previous; // 負邊緣偵測
        end
    end

	endmodule
  ```
2. 修正二
   * 利用 OR 來鎖定狀態，修正 out <= (~in) & in_previous; 這行
     ```verilog
     out <= out | ((~in) & in_previous);
     ```
   * 「現在的 out 等於（原本的 out 狀態）或上（剛剛發生的負邊緣）。」只要 out 的某個 bit 曾經變成了 1，因為 1 | 任何值 = 1，它就會一輩子被鎖定在 1，再也不會自己掉回 0。
* 修正後程式碼
  ```verilog
  module top_module (
    input clk,
    input reset,
    input [31:0] in,
    output [31:0] out 
	);
    
    // 用來記錄「上一個週期」輸入值的 32-bit 暫存器
    reg [31:0] d_last;

    always @(posedge clk) begin
        // 每個時脈正邊緣，更新「昨天的值」
        d_last <= in;
        
        // 同步重置與捕獲邏輯
        if (reset) begin
            out <= 32'b0; // reset 優先，全部清零
        end else begin
            // 若有下降沿就置 1，否則維持原狀（利用按位或 OR）
            out <= out | (~in & d_last);
        end
    end

	endmodule
  ```

## 關鍵知識/詞彙：
### 時序怎麼運作（以非阻塞賦值來看）
1. 當 posedge clk 來臨的一瞬間，pedge 會使用這一刻之前的 in 與 in_last 的舊值來做運算
2. 計算完 pedge 的同時，in_last 才會被更新為目前的 in
3. 這樣就在同一個時脈邊緣完成了「比較」與「存檔」兩個動作

### 範例 - Edgedetect（正邊緣偵測）時，in[0]的前一訊號為何？
* 程式
  ```verilog
  module top_module (
    input clk,
    input [7:0] in,
    output [7:0] pedge
	);
    
    reg [7:0]in_previous; // 用來儲存「上一個週期」輸入值的暫存器
    
    always@(posedge clk)begin
        pedge <= in & (~in_previous); //今天的 pedge 等於：今天為 1 且 昨天為 0
        in_previous <= in; //將今天的輸入值存進去，變成昨天的狀態，更新狀態
    end

	endmodule
  ```
* 在剛上電或剛開始執行時，如果我們沒有做任何處理，in_previous 的值在硬體上是「隨機、未知的（Undetermined / X）」。
  
1. 如果完全不重置（不給初始值），會發生什麼事？
    * 在實際的 FPGA 或晶片剛接通電源（上電）的那一瞬間，所有的 D 暫存器（包括 in_previous）裡面的電晶體會隨機倒向某一邊。
    * 在硬體模擬（Simulation）中，in_previous 的初始值會是 X (Unknown，未知值)。
    * 此時如果 in[0] 輸入是 0，而 in_previous[0] 是 X，會導致你的輸出 out 也是 X。直到時脈經過了第一個上升沿，把 in 的值存進in_previous 之後，電路才會開始正常工作。
  
2. 在 Verilog 中，我們如何解決「第一個狀態未知」的問題？
    * 利用 reset 訊號進行「強制初始化」（最標準的做法）
    * 在宣告時直接給予預設值（FPGA 常用）
       ```verilog
       reg [7:0] in_previous = 7'b0; // 上電時自動載入為 0
       ```

### 邊緣捕獲的關鍵要求
* 一旦某個 bit 偵測到下降沿（從 1 變 0），該 bit 的輸出 out 就必須一直鎖定在 1。就算後面沒有新的下降沿、就算 in 訊號一直變來變去，out 也必須牢牢記住「曾經發生過下降沿」這件事，直到按下 reset 為止。
---------------------------------------------
