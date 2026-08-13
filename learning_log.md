# 暑假自主研究與學習、Debug日誌
- **[點我回「README.md」](./README.md)**



<a id="toc"></a>

## 目錄（點日期跳轉）

| 日期 | 內容摘要 |
|---|---|
| [7/3](#m07d03) | 影片：TT 小教室第 1~5 課。 |
| [7/4](#m07d04) | 影片：TT 小教室第 6~7 課。 |
| [7/5](#m07d05) | 影片：TT 小教室第 8~11 課。 |
| [7/6](#m07d06) | 刷題：完成 HDLBits 的 "carry-slect Adder" 到 "a priority encoder for 8-bit inputs"。 |
| [7/7](#m07d07) | 影片：清大OCW王俊堯教授數位邏輯設計第 8B~8E 講。 |
| [7/8](#m07d08) | 資料：複習7/3 - 7/7進度。 |
| [7/9](#m07d09) | 影片：看財經村長-數位IC設計面試1。 |
| [7/10](#m07d10) | 資料：複習7/3 - 7/9內容。 |
| [7/11](#m07d11) | 資料：複習7/3 - 7/10內容。 |
| [7/13](#m07d13) | 資料：複習7/3 - 7/11內容。 |
| [7/14](#m07d14) | 刷題：完成 HDLBits 的 D latch 到 Implement a JK flip-flop with only a D-type flip-flop and gates（Exams/ece241 2013 q7）。 |
| [7/15](#m07d15) | 資料：複習7/3 - 7/14內容。 |
| [7/16](#m07d16) | 影片：TT 小教室 Verilog RTL design 進階教學【Coding Style】- 【Synchronizer】 |
| [7/17](#m07d17) | 影片：TT 小教室 Verilog RTL design 進階教學【Memory】 |
| [7/21](#m07d21) | 資料：複習7/3 - 7/17內容。 |
| [7/23](#m07d23) | 資料：利用網路資源學習Vivado - How to use vivado for Beginners by Anand Raj |
| [7/24](#m07d24) | 影片：財經村長 - 如何面試上一線數位IC公司 II |
| [7/25](#m07d25) | 影片：第1講 Vivado設計流程及使用模式；How to use Vivado® Design Suite Part-5 Timing Summary Report |
| [7/27](#m07d27) | 資料：Barrel Shifters in Verilog: A Beginner’s Guide to Fast Multi-Bit Shifting |
| [7/28](#m07d28) | 資料：ALU design in Verilog using MIPS Instruction Set |
| [7/30](#m07d30) | 刷題：複習 HDLBits 7/3 - 7/17 進度。 |
| [7/31](#m07d31) | 資料：MIT 6.111 課程講義 Lecture 9《Pipelining & Verilog》(PDF) |
| [8/1](#m08d01) | ALU 設計：32bit_ALU_V1（Baseline） - 改寫 32-bit ALU baseline 版本 testbench |
| [8/2](#m08d02) | ALU 設計：32bit_ALU_V1（Baseline） - 第一次 Synthesis + Implementation 結果，並記錄收斂到 WNS 接近 0 的結果 |
| [8/3](#m08d03) | ALU_V2（pipeline）設計：完成 RTL，共歷經 5 輪修正才達到邏輯正確 |
| [8/4](#m08d04) | ALU_V2（pipeline）設計：完成 Testbench - alu_v2_tt，驗證 pipeline 版本功能 |
| [8/5](#m08d05) | ALU 設計：32bit_ALU_V2（pipeline） - 第一次 Synthesis + Implementation 結果，並記錄收斂到 WNS 接近 0 的結果 |
| [8/10](#m08d10) | Digital Design and Computer Architecture (ARM / RISC-V Edition) |
| [8/11](#m08d11) | ALU 設計優化：主動重構，把邏輯運算與 SLT 先獨立拆成一個組合邏輯，Stage1 暫存器改成**每拍無條件更新** |
| [8/12](#m08d12) | 刷題：複習 HDLBits 7/3 - 8/11 進度；完成 HDLBits 的 Shift Registers |
| [8/13](#m08d13) | 影片：Digital Design and Computer Architecture(Spring 2025) L1 - L2 |


---

<a id="m07d03"></a>

## 2026 年 7 月 3 日

## 今日進度：
### 影片：[TT 小教室第 1~5 課](https://www.youtube.com/watch?v=W1DlwK63fxw&list=PLuhWBQnV46Q-3oMz33PFSqjhJOvDDVUbJ)
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
* 同步reset（reset隨clock動作）：例`always@(posedge clk)begin`
* 非同步reset（reset一來就動作）：例`always@(posedge clk or negedge rst_n)begin`//低電位非同步reset
  
### 震盪器（Oscillator）
* `always #<一半的週期時間> clk=~clk`（通常用在Testbench產生clk，一般數位電路不會這樣寫）

[回目錄](#toc)

---

<a id="m07d04"></a>

## 2026 年 7 月 4 日

## 今日進度：
### 影片：[TT 小教室第 6~7 課](https://www.youtube.com/watch?v=W1DlwK63fxw&list=PLuhWBQnV46Q-3oMz33PFSqjhJOvDDVUbJ)
### 刷題：複習 HDLBits 的 "basics" 到 "8-bit wide shift register of length 3(Three module)"。

## 遇到的困難與解決方案：
### 問題：在Connecting Signals to Module Ports的時候，搞錯"by position"與"by name"這兩種方法，by position與宣告順序有關。
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

**判斷 T/F，output 為 1bit**

### Reduction運算元：
  
 | 符號 | 名稱 |
 | --- | --- |
 | ＆ | Reduction AND |
 | ｜ | Reduction OR |
 | ︿ | Reduction XOR |

**將運算元右邊陣列中的每一位元做運算，output 為 1bit**
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
  * `assign a = 8'b1000_0011; -> a= -125 (2's complementary)`
  * `assign b = 4'b0010; -> b= +2`

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

[回目錄](#toc)

---

<a id="m07d05"></a>

## 2026 年 7 月 5 日

## 今日進度：
### 影片：[TT 小教室第 8~11 ](https://www.youtube.com/watch?v=W1DlwK63fxw&list=PLuhWBQnV46Q-3oMz33PFSqjhJOvDDVUbJ)
### 刷題：完成 HDLBits 的 "Adder1" 到 "Adder2"。

## 遇到的困難與解決方案：
### 問題1：
* Adder1（用兩個16位元加法器模塊合成出一個32位元加法器）
* 應題目需求：32位的加法器不需要處理進位（假設為 0）或出位（忽略)
<img width="528" height="342" alt="image" src="https://github.com/user-attachments/assets/ef7a65b8-8aba-43e0-82f1-93c8f5cb9194" />

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
  <img width="658" height="636" alt="image" src="https://github.com/user-attachments/assets/e6433c6c-69ac-486e-a1fa-9090a46052d1" />
  
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
<img width="520" height="258" alt="image" src="https://github.com/user-attachments/assets/bae8818a-7af7-491f-b103-b26f397f130d" />

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
* case、casez較常用，casez可用"?"、"z"表don't care
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
<img width="737" height="228" alt="image" src="https://github.com/user-attachments/assets/9d222c2a-40cc-4148-91f3-f1a0f6d6e910" />

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
<img width="1028" height="68" alt="image" src="https://github.com/user-attachments/assets/274cd8dd-74c6-4a1b-a03c-b43b4b29b6ec" />

* **有時候在互傳數據時，習慣有的會先傳高位，有的先傳低位**

### define
* 通常被用來定義一些常數或是程式碼的開關
* 可以橫跨所有 modules 和 Hierarchy
* 使用時於名稱前面加一撇（註：即反單引號 `）

| 特性 |  `define` (全球巨集) | `parameter` (局部參數) |
| --- | --- | --- |
| **語法關鍵字** | 開頭帶有反單引號，如 `'define DATA_WIDTH 8` | 正常宣告，如 `parameter DATA_WIDTH = 8;` |
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
<img width="538" height="64" alt="image" src="https://github.com/user-attachments/assets/91351fab-1534-4bb7-bfe7-58012261b5af" />

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
    parameter 名稱1 = 值1,
    parameter 名稱2 = 值2
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
module test_parameter_0#(
    parameter A_BW = 8,
    parameter B_BW = 4,
    parameter Z_BW = B_BW + A_BW
)
(
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
<img width="857" height="99" alt="image" src="https://github.com/user-attachments/assets/d838dbc9-fddc-4462-bb0f-85065e9d300a" />

* **state machine建議都使用parameter來寫，可讀性較高**

### define 與 parameter 有什麼不同？
| 特性 | define (全球巨集) | parameter (局部參數) |
| --- | --- | --- |
| **語法關鍵字** | 開頭帶有反單引號，如 define DATA_WIDTH 8 | 正常宣告，如 parameter DATA_WIDTH = 8; |
| **作用範圍** | **全域（Global）**。只要在編譯順序中被讀取，其後所有的 .v 檔案、所有 Module 都能直接使用。 | **區域（Local）**。只在宣告它的該個 module 內部有效。 |
| **使用方式** | 呼叫時前面一定要加一撇 | |

[回目錄](#toc)

---

<a id="m07d06"></a>

## 2026 年 7 月 6 日

## 今日進度：
### 刷題：完成 HDLBits 的 "carry-slect Adder" 到 "a priority encoder for 8-bit inputs"。

## 遇到的困難與解決方案：
### 問題：
* carry-slect Adder（選擇式加法器）
* **使用的位元範圍q0[31:16]、q1[31:16]超出了它原本宣告的[15:0]範圍，位元寬度（或範圍）越界錯誤導致編譯錯誤。**
<img width="551" height="376" alt="image" src="https://github.com/user-attachments/assets/0530f38b-d2fe-4996-aa6c-43adb3870768" />

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
<img width="943" height="105" alt="image" src="https://github.com/user-attachments/assets/00830233-3492-4dec-89df-450df98e20ef" />

  ```verilog
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

[回目錄](#toc)

---

<a id="m07d07"></a>

## 2026 年 7 月 7 日

## 今日進度：
### 影片：[清大OCW王俊堯教授數位邏輯設計第 8B~8E ](https://www.youtube.com/watch?v=fW6xAPTOMm0&list=PLS0SUwlYe8czJbz5-sRtbuTleObQE9mOa)
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

[回目錄](#toc)

---

<a id="m07d08"></a>

## 2026 年 7 月 8 日

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

[回目錄](#toc)

---

<a id="m07d09"></a>

## 2026 年 7 月 9 日

## 今日進度：
### 影片：[看財經村長-數位IC設計面試 ](https://www.youtube.com/watch?v=yATdPIrKdug&t=2092s)
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
<img width="960" height="246" alt="image" src="https://github.com/user-attachments/assets/e2810095-f470-4a6b-b27d-3914b8dbbbb6" />

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
<img width="196" height="286" alt="image" src="https://github.com/user-attachments/assets/60c7747b-b0f1-46a7-b6f7-c8995f339fe5" />

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
<img width="400" height="158" alt="image" src="https://github.com/user-attachments/assets/5220f383-c71d-47c3-b344-c70dfdb6acbb" />

* Setup Time
  * Setup time(Tsu)是指在時鐘有效邊緣(例如上升沿)到來之前，資料輸入端(D)的信號必須保持穩定不變的最短時間。確保Flip-flop內部的主Latch能夠正確地採樣並鎖存輸入資料。
  * Setup time的長短取決於Flip-flop內部電路的速度特性。
  
* Hold Time
  * Hold time(Th)是指在時鐘有效邊緣(例如上升沿)到來之後，資料輸入端(D)的信號必須繼續保持穩定不變的最短時間。確保Flip-flop能夠完全穩定地鎖存資料，新資料不會過早到達而破壞正在被捕捉的資料。
  * 通常比setup time短得多
  * Hold violation(保持時間違規)發生在資料路徑延遲太短，新資料過早到達的情況。與時鐘週期無關，通常需要透過插入延遲(如buffer)來修復。

### clock skew（時鐘偏斜的影響與計算）
<img width="231" height="239" alt="image" src="https://github.com/user-attachments/assets/15418ac1-440d-420c-8644-a10223246c89" />
<img width="337" height="313" alt="image" src="https://github.com/user-attachments/assets/ba0c8bac-59a2-4208-9c7e-76f5280483e4" />


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
<img width="453" height="265" alt="image" src="https://github.com/user-attachments/assets/627fc495-9eba-49e8-91e5-33d86bc13a93" />

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

[回目錄](#toc)

---

<a id="m07d10"></a>

## 2026 年 7 月 10 日

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

[回目錄](#toc)

---

<a id="m07d11"></a>

## 2026 年 7 月 11 日

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

[回目錄](#toc)

---

<a id="m07d13"></a>

## 2026 年 7 月 13 日

## 今日進度：
### 資料：複習7/3 - 7/11內容。
### 刷題：完成 HDLBits 的 Karnaugh Map to Circuit 到 DFF with byte enable。

## 遇到的困難與解決方案：
### 問題：
### using one 4-to-1 multiplexer and as many 2-to-1 multiplexers as required
<img width="388" height="361" alt="image" src="https://github.com/user-attachments/assets/511aa44c-348d-41be-bb10-6f97ac0a3164" />

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
<img width="932" height="113" alt="image" src="https://github.com/user-attachments/assets/8b8a3b2d-b80d-4c60-beae-c8e60469a240" />

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

[回目錄](#toc)

---

<a id="m07d14"></a>

## 2026 年 7 月 14 日

## 今日進度：
### 刷題：完成 HDLBits 的 D latch 到 Implement a JK flip-flop with only a D-type flip-flop and gates（Exams/ece241 2013 q7）。

## 遇到的困難與解決方案：
### 問題：
### Exams/ece241 2014 q4
* z 的輸出邏輯錯誤 (Timing Bug)： z 是一個純組合邏輯，它是直接接在暫存器輸出 Q 後面的 NOR 閘。
* 將 z 一併寫在循序邏輯電路中，造成 output z 資料獲取比實際慢一個 D 正反器的時脈週期
<img width="463" height="345" alt="image" src="https://github.com/user-attachments/assets/41b0f37d-3c79-4e73-bdde-d185887ceab1" />

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

[回目錄](#toc)

---

<a id="m07d15"></a>

## 2026 年 7 月 15 日

## 今日進度：
### 資料：複習7/3 - 7/14內容。
### 刷題：複習 HDLBits 的 "Modules" 到 "Implement a JK flip-flop with only a D-type flip-flop and gates（Exams/ece241 2013 q7）"；完成 HDLBits - sequential logic。

## 遇到的困難與解決方案：
### 問題：
### Edgedetect（正邊緣偵測）
* 不理解邊緣偵測的核心硬體思想，最初思想為當clk正緣發生且有資料(in)輸入(資料為真)則邊緣偵測(pedge)為真。

### Edgecapture（邊緣捕獲暫存器）
<img width="647" height="232" alt="image" src="https://github.com/user-attachments/assets/87135924-2e42-482c-9537-fdf29bedc062" />

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
        end
  		else begin
            out <= (~in) & in_previous; //只有在發生負邊緣的那一個週期 out 會是 1，下一個週期如果 in 維持 0（沒有新的邊緣），out 就會立刻自動變回 0。
        end
    end

	endmodule
  ```

### 解法：
### Edgedetect（正邊緣偵測）
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
        in_previous <= in; //每個時脈週期都把當前的 in 存下來，避免 reset 撤銷時產生假的邊緣
        if (reset) begin
            out <= 32'b0;
        end
  		else begin
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
   * 「現在的 out 等於原本的 out 狀態。」只要 out 的某個 bit 曾經變成了 1，因為 1 | 任何值 = 1，它就會鎖定在 1，不會自己掉回 0。
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
        end
  		else begin
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
<img width="607" height="153" alt="image" src="https://github.com/user-attachments/assets/28c5e5b1-9c17-43e9-a91a-50656969ae26" />

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

### 雙邊沿觸發器（Dual-edge）
1. 它是以「半個時脈週期（Half-Cycle）」為單位的延遲
   * 只要 clk 轉折一下（半個週期），資料就會被傳遞過去。
   * 所以它造成的延遲，在時間軸上被縮短成了半個時脈週期。這讓資料能以兩倍的速度（Double Data Rate, 如我們熟知的 DDR 記憶體）向前推進！
     
2. 「延遲」在硬體中其實是保護機制
   * 讓前面的組合邏輯有充足的時間去運算，最後穩定下來。
   * 等到 clk 邊緣（上升沿或下降沿）來臨，暫存器才會抓取當下最穩定的正確資料，並延遲到下一刻。

3. 今日範例
<img width="300" height="102" alt="image" src="https://github.com/user-attachments/assets/6bb186e6-b66c-4a64-97f6-f08b99a87d10" />

   ```verilog
   module top_module (
    input clk,
    input d,
    output q
	);
    reg q_pos;
    reg q_neg;

    // 上升沿時，把 (d ^ q_neg) 存進 q_pos
    always @(posedge clk) begin
        q_pos <= d ^ q_neg;
    end

    // 下降沿時，把 (d ^ q_pos) 存進 q_neg
    always @(negedge clk) begin
        q_neg <= d ^ q_pos;
    end

    // 最終輸出 q 永遠是兩個暫存器 XOR 的結果
    assign q = q_pos ^ q_neg;

	endmodule
   ```
   
   * 程式運作原理：
     * 當「上升沿」來臨時：q_pos 變成了 d ^ q_neg。此時輸出的 q = q_pos ^ q_neg 就變成了 (d ^ q_neg) ^ q_neg。根據 XOR 特性，兩個 q_neg 互相抵銷了！所以 q 瞬間變成了 d！
     * 當「下降沿」來臨時：q_neg 變成了 d ^ q_pos。此時輸出的 q = q_pos ^ q_neg 就變成了 q_pos ^ (d ^ q_pos)。同理，兩個 q_pos 互相抵銷了！所以 q 也瞬間變成了 d！

5. 應用
   * DDR 記憶體 (Double Data Rate SDRAM) ── 最著名的應用
     * 應用方式： 記憶體晶片與 CPU 之間的資料匯流排（Data Bus），就是利用雙邊沿觸發技術。
     * 效果： 如果記憶體時脈是 1600 MHz，傳統單邊沿（SDR）只能達到 1600 Mbps 的傳輸率；而改用雙邊沿觸發後，在同一個時脈下，傳輸率直接翻倍變成 3200 Mbps！這也是為什麼我們買記憶體時，標示的頻率通常是實際時脈的兩倍。

   * 行動裝置與綠色晶片 ── 節省 50% 的時脈功耗
     * 省電絕招：如果我們把晶片內部的 DFF 全部換成「雙邊沿觸發（DETFF）」，就可以將時脈頻率直接砍半（例如從 200 MHz 降到 100 MHz），但晶片的運算效能完全保持不變！
     * 效果： 時脈頻率減半，直接讓時脈樹的功耗省下將近一半，手機續航力大幅提升。

   * 高速序列傳輸介面 (SerDes / High-Speed Serial)
     * 應用方式： 為了降低晶片內部時脈產生器（PLL/DLL）的設計難度，工程師會讓發送端與接收端改用雙邊沿來抽樣資料。
     * 效果： 我們只需要產生 10 GHz 的時脈，就能傳輸 20 Gbps 的資料流，大大降低了高頻類比電路的設計門檻。

6. 問題 - 為什麼不把晶片裡的暫存器全部改成雙邊沿？
   * 對時脈「工作週期（Duty Cycle）」要求極苛刻：
     * 單邊沿觸發只看上升沿，所以時脈訊號是「High 佔 60%、Low 佔 40%」還是五五開，完全不影響系統運作。
     * 但雙邊沿觸發同時看兩邊。如果時脈工作週期不是完美的 50%，那麼「奇數週期」和「偶數週期」的時間長度就會不一樣，這會導致電路的時序分析（Static Timing Analysis）變得極其痛苦，非常容易出錯。

   * 硬體成本與設計難度：
     * 標準的 FPGA 或標準元件庫（Standard Cell Library）裡，雙邊沿暫存器的電路結構比單邊沿複雜得多（面積大、放線難），因此只會用在記憶體、傳輸介面等「最需要衝極速、省功耗的刀口上」。

[回目錄](#toc)

---

<a id="m07d16"></a>

## 2026 年 7 月 16 日

## 今日進度：
### 影片：[TT 小教室 Verilog RTL design 進階教學【Coding Style】- 【Synchronizer】](https://www.youtube.com/watch?v=U1ZQzLmoARM&list=PLuhWBQnV46Q92gAT-KvzdJUhBh3maNkFF)
### 刷題：完成 HDLBits - counters 的 Four-bit binary counter  到 slow decade counter。

## 遇到的困難與解決方案：
### 問題：
### slow decade counter
* 將同步致能訊號與同步、非同步 reset 觀念搞混

### 解法：
1. 非同步重置(Asynchronous Reset)
   * 運作機制： 只要重置按鈕一按下去，電路立刻歸零，完全不管時脈有沒有在跑
   * 優點：
     * 不需要時脈（Clock-less）： 晶片剛上電時，時脈產生器（PLL）可能還沒穩定（甚至根本還沒開始送時脈）。此時，非同步重置可以在沒有時脈訊號的情況下，直接把整顆晶片初始化到安全狀態
     * 節省面積： 大部分晶片底層的標準元件（Standard Cell）或 FPGA 的 D 暫存器，硬體內部本來就內建了一根「非同步重置」的實體接腳（Clear Pin）。直接使用它不需要額外的邏輯閘
   * 缺點：
     * 非同步釋放帶來的危機： 當你要放開重置（Reset Release）時，如果放開的瞬間剛好卡在時脈的上升沿，暫存器會陷入 「亞穩態（Metastability）」。這會導致晶片當機或輸出亂碼
       
2. 同步重置 (Synchronous Reset)
   * 運作機制： 重置訊號按下去後什麼事都不會立刻發生，必須等到 下一個時脈正緣 來臨，電路才會重置
   * 優點：
     * 百毒不侵： 因為它由 clk 統一過濾，所以重置訊號上的毛刺（Glitches，小雜訊）會被時脈自動濾除
     * 100% 同步： 整個晶片的所有暫存器都在同一個時脈緣一起重置、一起釋放，絕對不會有亞穩態問題，時序分析（STA）非常簡單
   * 缺點：
     * 必須有時脈： 如果時脈因為意外停掉（Clock Gate 關閉或 PLL 沒準備好），這個重置就完全失效了。
     * 浪費電路面積： 如果硬體暫存器本身沒有同步重置腳位，編譯器必須在 D 暫存器的輸入端前多塞一個 AND 閘來實現同步重置，這會增加晶片面積和延遲

## 關鍵知識/詞彙：
### 一、Coding Style
1. 訊號取名
   * 取名規則：
     * 名字要有意義，讓大家都看得懂訊號代表的意思。
     * input: i_xxx
	 * output: o_xxx
     * wire: w_xxx
     * reg: r_xxx
     * state machine: *_ cs, *_ curr_state, *_ ns, *_ next_state
     * 訊號、10、module名稱用小寫；常數、parameter、define用大寫。
     * 傳輸訊號名稱加上prefix(前綴)，o_aa2bb _* 代表從block“aa”到block“bb”的訊號
     * *_ n或 *_ b代表active low訊號
     * 一個檔案一個module，檔名就是module name
     * instant name建議是u_module_name
   * 避免使用
     * n+數字、n123等
     * vdd、gnd、vss保留給power、ground使用

2. always block
   * Combionational Logic
     ```verilog
     always @ (*) begin
		// logic uses blocking "="
	 end
     ```
   * Sequential logic
     ```verilog
     always @ (posedge i_clk or negedge i_rst_n) begin
	 	if (!i_rst_n) begin
	 	//reset the DFF
	 	end
     	else begin
	 	// DFF logic uses non-blocking " <= "
	 	end
	 end
     ```
   * RTL裡不要用任何#delay
   * Combinational就是這個cycle出值；Sequential就是下一個cycle出值
   * 避免combinational loop（把自己產生的組合邏輯又餵回給自己用來產生那個邏輯）

3. DFF（D Flip-flop）
   ```verilog
   always @ (posedge i_clk or negedge i_rst_n) begin
   		if (!i_rst_n)
   			//reset the DFF
   		else if
   		// DFF logic uses non-blocking " <= "
   end
   ```
   * 注意DFF的clock和reset是誰
   * DFF要有reset，條件要寫滿，以免變成Latch
   * clock和非同步reset在scan時一定要可控(controllable)，如果不行要加scanmux
   * 盡量整個design都用一樣的clock edge
   * 盡量避免 clock 連到 DFF 的 data pin
   * 注意發送端和接收端的 clock 是否一樣，不一樣要處理 CDC（clock domain crossing）的問題
   * 最後一個else最好不要賦新值（assign 回自己或完全不寫），這樣可以讓合成工具自動插入clock gating
   * DFF array太寬和太深時（例如超過1000個DFFs）建議改用memory，面積比較划算，繞線比較沒問題。e.g.reg[255:0]r_array[1024];
     
4. State Machine
   ```verilog
   parameter IDLE = 1'b0;
   parameter RUN = 1'b1;

   always @ (posedge i_clk or negedge i_rst_n) begin
   		if (!i_rst_n)
   			r_xxx_cs <= IDLE;
   		else
   			r_xxx_cs <= r_xxx_ns;
   end

   always @(*) begin
   		case (r_xxx_cs)
   		IDLE:
   			if (i_start) r_xxx_ns = RUN;
   			else r_xxx_ns = IDLE;
   		RUN:
   			if (i_stop) r_xxx_ns = IDLE;
   			else r_xxx_ns = RUN;
   			default: r_xxx_ns = IDLE;
   		endcase
   end
   ```
   * States 用 parameter 定義
   * current state要有 reset 條件
   * current state 在 non-blocking 的 always block 裡 assign 成 next state
   * next state 在 blocking 的 always block 裡用 case 靠 current state 來決定
   * 每一個 state 都要寫到，先寫跳出當下 state 的條件，最後一個 else 就是停留在當下的 state
   * next state 記得加上 default 條件，回到 IDLE 狀態

5. Block 之間
   * input signals通常可以直接用，但邏輯不要太深，除非 timing 真的很差，再 Flop 後使用
   * output signals 建議 Flop 後再給其他blocks
   * 不要把邏輯寫在 pin 的連接上 e.g.i_a(w_b&w_c);
   * 整個 design 都用到的常數用 define 定義，部分design的參數用 parameter 傳遞
   * 善用前輩設計好的design，例如FIFO、synchronizer。
   * 善用comment，尤其在input/output上，讓別人也看得懂意思。

### 二、Synchronizer
1. 簡介、用途
  * 同步器，Synchronizer是SoC裡常見的元件
  * 處理不同clock之間的訊號接收
  * 同步數位電路都是由clock驅動，電路在同一個時鐘邊沿(clock edge)工作
  * SoC裡可以有許多不同步的時鐘，有得快，有得慢
  * 訊號需要從某一個時鐘域(Clock domain)傳遞到另一個時鐘域，術語稱為 CDC(Clock Domain Crossing)，就需要同步器。

2. Setup Time & Hold Time
<img width="912" height="400" alt="image" src="https://github.com/user-attachments/assets/c93d6b8b-74d1-4ae3-84a4-b7cb89a23bd4" />
 
3. Metastability（亞穩態）
<img width="699" height="239" alt="image" src="https://github.com/user-attachments/assets/bc8d7b65-5a4a-4a4f-85c1-f6b5bd0f2cbc" />
<img width="502" height="330" alt="image" src="https://github.com/user-attachments/assets/fd4bec73-800b-4167-a471-5bc3d61a4073" />

   * 當一個DFF的 setup time 或 Hold time 不滿足時，它的Q將"不可預測"，稱為 Metastability 亞穩態。
   * 沒有辦法可以"完全"解決，只能大幅度降低產生的機率。
   * MTBF(mean time between failure)意思是發生兩次錯誤之間的間隔，這個指標常用來衡量CDC的情形，越大越好（代表隔了很長一段時間才發生下一次錯誤）。   

[回目錄](#toc)

---

<a id="m07d17"></a>

## 2026 年 7 月 17 日

## 今日進度：
### 影片：[TT 小教室 Verilog RTL design 進階教學【Memory】](https://www.youtube.com/watch?v=U1ZQzLmoARM&list=PLuhWBQnV46Q92gAT-KvzdJUhBh3maNkFF)
### 刷題：完成 HDLBits 的 "counter 1-12" 到 "4-digit BCD counter (Countbcd)"；待完成 "12-hour clock"。

## 遇到的困難與解決方案：
### 問題：
### 4-digit BCD counter (Countbcd)
* q[15:0]個別拆成4個bit（q[3:0]、q[7:4]...、q[15:12]）計算的用意為何
<img width="938" height="230" alt="image" src="https://github.com/user-attachments/assets/bacd286d-dcd8-42c1-a779-9a115c71b3da" />

* 原程式碼
  ```verilog
  module top_module (
    input clk,
    input reset,   
    output [3:1] ena,
    output [15:0] q);
    
    
    assign ena[1] = q[3:0] == 4'd9; //當個位數字counter數到9，十位數字counter致能
    assign ena[2] = (q[3:0] == 4'd9 && q[7:4] == 4'd9);//當個位、十位數字counter數到9，千位數字counter致能
    assign ena[3] = (q[3:0] == 4'd9 && q[7:4] == 4'd9 && q[11:8] == 4'd9 );//當個位、十位、百位數字counter數到9，千位數字counter致能
    
    //構建個、十、百、千位數字counter
    counter10 inst0(
        .clk(clk),
        .reset(reset),
        .enable(1), //個位數字永遠致能
        .q(q[3:0])
    );
    
    counter10 inst1(
        .clk(clk),
        .reset(reset),
        .enable(ena[1]),
        .q(q[7:4])
    );
    
    counter10 inst2(
        .clk(clk),
        .reset(reset),
        .enable(ena[2]),
        .q(q[11:8])
    );
    
    counter10 inst3(
        .clk(clk),
        .reset(reset),
        .enable(ena[3]),
        .q(q[15:12])
    );
    

	endmodule

	module counter10( //構建0 到 9 的計數（BCD 計數器）
    	input clk,
    	input reset,
    	input enable,
    	output reg [3:0] q);
    
    	always@(posedge clk)begin
        	if(reset)begin
            	q <= 4'd0;
        	end
        	else if(enable)begin  
            	q <= (q < 4'd9) ? q + 4'b0001 : 0;
        	end
    	end

	endmodule
  ```
### 解法：
### 4-digit BCD counter (Countbcd)
* 這題考的是 BCD（Binary-Coded Decimal，二進位碼十進位）。簡單來說，這是一種「**用二進位的外殼，強行裝載十進位靈魂**」的設計方式。
* 如果你**不拆開**，直接把 q 當成一個普通的 16-bit 二進位計數器，對人類習慣的十進位顯示器（例如七段顯示器）來說**必須額外寫一個極其複雜的「二進位轉十進位（Binary to BCD）」數學電路**，會大幅消耗晶片的面積與效能。
* 拆開計算有三大優勢：
  * 4位元剛好裝得下數字 9
  * 直覺對應人類的顯示介面（晶片外面負責接「七段顯示器」的電路，完全不需要做任何數學運算，直接顯示對應數字）
  * 硬體設計的「模組化與級聯」（只需要設計一個 counter10 子模組（只管 0~9），然後複製 4 次（個、十、百...），再用組合邏輯（ena）把他們串起來）- 分治法（Divide and Conquer）

## 關鍵知識/詞彙：
### Exams/ece241 2014 q7a - Design a 1-12 counter
<img width="943" height="481" alt="image" src="https://github.com/user-attachments/assets/378391f6-addb-4e2c-971a-c945ba41638a" />

* 程式碼：
  ```verilog
  module top_module (
    input clk,
    input reset,
    input enable,
    output [3:0] Q,
    output c_enable,
    output c_load,
    output [3:0] c_d
	); //

	//實例化題目提供的 4-bit 內建計數器
    count4 my_counter(
        .clk(clk),
        .enable(c_enable),
        .load(c_load),
        .d(c_d),
        .Q(Q)
    );
    assign c_enable = enable; //產生要送往子模組 count4 的控制訊號
    assign c_load = reset | ((Q == 4'd12) && enable); // 當 reset 為 1，或者（目前是 12 且 enable 為 1 要往上加時），就要觸發 load
    assign c_d = 4'd1; //c_load為 1 時動作，不論是 reset 還是從 12 回彈，目標值都是 1

	endmodule
  ```

### Exams/ece241 2014 q7b - create a digital wall clock
<img width="943" height="296" alt="image" src="https://github.com/user-attachments/assets/0944c545-e2cf-414b-90e9-0960f35a4a58" />

* 程式碼：
  ```verilog
  module top_module (
    input clk,
    input reset,
    output OneHertz,
    output [2:0] c_enable
	); // 
    
    wire [3:0]q0, q1, q2;
    
    assign c_enable[0] = 1'd1; //個位數計數永遠致能
    assign c_enable[1] = (q0 == 4'd9); //個位數數到9，十位計數致能
    assign c_enable[2] = (q0 == 4'd9 && q1 == 4'd9);////個位數數到9，十位數數到9，百位計數致能

  //構建 3 台counter
    bcdcount inst0(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[0]),
        .Q(q0)
    );
    
    bcdcount inst1(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[1]),
        .Q(q1)
    );
    
    bcdcount inst2(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[2]),
        .Q(q2)
    );

    assign OneHertz = (q0 == 4'd9 && q1 == 4'd9 && q2 == 4'd9 ); //當數字為999時輸出1，每隔1000 cycle出現999一次，觸發一次（1Hz）之後就歸0正好1000Hz/1000=1Hz
    
	endmodule
  ```

### Memory
1. 簡介
   * SoC裡必要的元件
   * 用來"記憶"：資料、計算過程、狀態等
   * 所占的面積甚至可以超越組合邏輯的面積
   * 設計數位電路時需要考慮：
     * 儲存的架構：用哪一種型態的儲存器？分享還是專用？
     * 如何讀寫資料：1讀1寫、2讀、2寫、只讀不寫
     * bandwidth是否滿足throughput：每個 cycle 能獲得多少資料？
     * Latency（延遲）: 1 cycle, 2 cycles, ...
     * 接口的規範：AXI；push／pop；rd_en／wr_en

2. Memory 型態

| 型態 | 每個cycle讀/寫 | Reset | Latency | 速度 | 面積 | 功耗 |
| --- | --- | --- | --- | --- | --- | --- |
| DFF Array | N讀N寫 | 1 cycle | 0 | 最快 | 最大 | 最大 |
| FIFO | 1讀1寫 | 1 | 0/1 | 次快 | 大 | 大 |
| 1 port SRAM | 1讀 or 1寫 | N | 1 | 快 | 小 | 小 | 
| 2 port SRAM | 1讀1寫 | N | 1 | 可 | 中 | 中 |
| Dual port SRAM | 1讀1寫 or 2讀 or 2寫 | N/2 | 1 | 慢 | 大 | 大 |
| ROM | 1讀 | 不能 | 1 | 慢 | 小 | 小 |
* N：儲存空間的深度

3. DFF Array
   * 宣告一個2-Dimensional reg就是一個DFF array
     * reg [WIDTH-1:0] dff_array[DEPTH];
     * WIDTH是這個array的寬度：用位寬選
     * DEPTH是這個array的深度：用地址選
   * 每個clock cycle都可以讀寫任一個地址的任意位寬
   * rd _* /wr _* 可以擴充組數增加bandwidth
   * 寫：
     ```verilog
     generate
	 for (geni=0; geni<`DEPTH; geni=geni+1) begin
     always @(posedge i_clk or negedge i_hrst_n) begin
	 	if (~i_hrst_n)
	 		dff_array[geni] <= 1'b0;
	 	else if (wr_en && (wr_addr == geni))
	 		dff_array[geni] <= wr_data;
     		end
     	end
     endgenerate
     ```
     * 讀：
       ```verilog
       assign rd_data = dff_array[rd_addr];
       ```
     * 在設計時深度不要太大（想像讀取時是一個巨大的MUX去選出要讀取的data），通常建議 [WIDTH]*[DEPTH] 不要上萬
   
4. FIFO（First in First out）
   * 有一個入口和一個出口，一進一出，先進先出的儲存元件
   * 讀是pop；寫是push；與clock同步
   * 有read/write兩個同步的指標(rd_ptr/wr_ptr)，ptr是指pointer
   * push：(wr_ptr + 1) % (FIFO_DEPTH) //寫入之後再把 wr_ptr + 1
   * pop: (rd_ptr + 1) % (FIFO_DEPTH) //讀取之後再把 rd_ptr + 1
   * rd_ptr == wr_ptr：FIFO是空的(empty) //**資料無法pop**，會造成"underflow"
   * rd_ptr == (wr_ptr+1)%(FIFO_DEPTH)：FIFO 是滿的(full) //**資料無法再push**，會造成"overflow"
   * diff是FIFO裡資料的個數。選擇性的output
   * FIFO內部可以使用DFF array實現，也可以使用2port SRAM實現。
   * I/O宣告：
     ```verilog
     module fifo
	 #(
     parameter FIFO_WIDTH = 16,
	 parameter FIFO_DEPTH = 4,
	 parameter FIFO_DEPTH_BW = 2
     )
     
	 (
	 input i_clk,
	 input i_hrst_n,
	 output o_empty,
	 output o_full,
	 output [FIFO_DEPTH_BW:0] o_diff,
	 input i_push,
	 input [FIFO_WIDTH-1:0] i_push_data,
	 input i_pop,
	 output [FIFO_WIDTH-1:0] o_pop_data
     );
     ```

5. SRAM
   * static random-access memory 靜態隨機存取記憶體
   * 用在需要"大量儲存資料"或是"計算過程與結果"的地方
   * 只要保持通電，儲存的資料就可以恆常保持，斷電後，SRAM儲存的資料就消失
   * 廠商提供Memory Compiler，生成不同種類的SRAM給不同的應用
   * 通常單片SRAM都有長寬比例與最大值的限制
    
6. 應用方式
   *  DFF array：
     * 隨時存取大量的資料、超高頻寬、超小Latency
     * 儲存的資料量不大，例如小於1萬bits
       
   * FIFO
     * 資料排隊依序傳送
     * 可以適當增加bandwidth，一次push或pop多筆資料
     * 非同步FIFO可以處理CDC問題

   * 1 port SRAM
     * 同一週期只需1讀或1寫的操作
     * 節省面積
       
   * 2 port SRAM
     * 同一週期需要1讀且1寫的操作
     * 加大位寬以增加頻寬
     * 處理CDC問題
     * 節省面積
     * Timing 較差
       
   * Dual port SRAM
     * 同一週期需要2讀或2寫的操作
     * 先進製程(<40nm)盡可能避免使用此類型
     * 面積大

   * ROM
     * 事先決定內容，不可改
     * 斷電後內容不消失
     * CPU Boot ROM：開機CPU就去讀（例如：初始化）

### 待完成
```verilog
module top_module(
    input clk,
    input reset,
    input ena,
    output pm,
    output [7:0] hh,
    output [7:0] mm,
    output [7:0] ss); 
    
    wire [5:0] c_enable;
    assign c_enable[0] = 1'b1;
    assign c_enable[1] = (s0[3:0] == 9);
    assign c_enable[2] = (s0[3:0] == 9 && s1[7:4] == 5);
    assign c_enable[3] = (s0[3:0] == 9 && s1[7:4] == 5 && m0[3:0] == 9);
    assign c_enable[4] = (s0[3:0] == 9 && s1[7:4] == 5 && m0[3:0] == 9 && m1[7:4] == 5);
    assign c_enable[5] = (s0[3:0] == 9 && s1[7:4] == 5 && m0[3:0] == 9 && m1[7:4] == 5 && h0[3:0] == 9);
    assign pm = (s0[3:0] == 9 && s1[7:4] == 5 && m0[3:0] == 9 && m1[7:4] == 5 && h0[3:0] == 1 && h1[7:4] == 1);
    
    counter9 s0(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[0]),
        .ss(ss[3:0])
    );
    
    counter5 s1(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[1]),
        .ss(ss[7:4])
    );
    
    counter9 m0(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[2]),
        .mm(mm[3:0])
    );
    
    counter5 m1(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[3]),
        .mm(mm[7:4])
    );
    
    counter9 h0(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[4]),
        .hh(hh[3:0])
    );
    
    counter2 h1(
        .clk(clk),
        .reset(reset),
        .enable(c_enable[5]),
        .hh(hh[7:4])
    );

endmodule

module counter9(
    input clk,
    input reset,
    input enable,
    output reg [3:0] q);
    
    always@(posedge clk)begin
        if(reset)begin
            q <= 4'd0;
        end
        else if(enable)begin  
            q <= (q < 4'd9) ? q + 4'b0001 : 0;
        end
    end
    
endmodule

module counter5(
    input clk,
    input reset,
    input enable,
    output reg [3:0] q);
    
    always@(posedge clk)begin
        if(reset)begin
            q <= 4'd0;
        end
        else if(enable)begin  
            q <= (q < 4'd5) ? q + 4'b0001 : 0;
        end
    end
    
endmodule

module counter2(
    input clk,
    input reset,
    input enable,
    output reg [3:0] q);
    
    always@(posedge clk)begin
        if(reset)begin
            q <= 4'd0;
        end
        else if(enable)begin  
            q <= (q < 4'd2) ? q + 4'b0001 : 0;
        end
    end
    
endmodule
```

[回目錄](#toc)

---

<a id="m07d21"></a>

## 2026 年 7 月 21 日

## 今日進度：
### 資料：複習7/3 - 7/17內容。
### 刷題：複習 HDLBits 7/3 - 7/17 進度。

[回目錄](#toc)

---

<a id="m07d23"></a>

## 2026 年 7 月 23 日

## 今日進度：
### 資料：利用網路資源學習Vivado - How to use vivado for Beginners by Anand Raj
### Vivado：嘗試在 Vivado 上寫簡單半加器模型並模擬測試

## 今日成果探討：
### half-adder
* Design sources
```verilog
module half_adder(

input a,b,
output carry,
output sum

    );
    
    assign sum = a ^ b;
    assign carry = a & b;
    
endmodule
```

* Simulation sources
```verilog
module half_adder_tb();

reg t_a, t_b; //輸入用reg
wire Sum, Carry; //輸出用wire

// 實例化
half_adder inst0( 
    .a(t_a),
    .b(t_b),
    .sum(Sum),
    .carry(Carry)
);
```

* 模擬結果
<img width="780" height="415" alt="image" src="https://github.com/user-attachments/assets/b1f33562-5125-4ad4-8991-89a545118c93" />

## 關鍵知識/詞彙：
### Testbench（測試平台）
1. 提供驅動訊號（時脈與輸入資料）
* 硬體電路是不會自己運作的。Testbench 可以模擬真實世界中的外部輸入例如：
	* 自動生成規律震盪的 Clock（時脈）
    * 觸發系統開機的 Reset（重置訊號）
    * 按時間順序餵入各種測試資料（如 10ns 時輸入數字 5，20ns 時輸入數字 10）

2. 驗證邏輯功能（不用每次都燒錄進晶片）
* 將代碼燒錄進 FPGA 晶片通常需要花費 幾十分鐘到數個小時
    * 沒有 Testbench： 每改一行代碼，就要花一小時燒錄，再用示波器抓訊號，效率極低
    * 使用 Testbench： 在電腦上跑模擬只需要幾秒鐘，就能透過波形圖（Waveform）即時查看晶片內部的每一個運算結果

3. 自動化測試與自我檢查（Self-Checking）
* 可以寫入預期結果（Golden Model），當電路輸出錯誤時，Testbench 會自動在終端機列印

4. Testbench 與一般RTL電路的差別

| 特性 | 一般 RTL 電路 (Design) | 測試平台 Testbench |
| --- | --- | --- |
| 最終去處 | 會被合成門陣列，燒錄進晶片 | 只在電腦軟體中執行，不會變成實體晶片 |
| 最終去處 | 嚴格，只能寫「可合成 (Synthesizable)」語法 | 寬鬆，可以使用延遲 #、印出文字 $display 等模擬專用語法 |
| 輸入輸出 | 有 input 與 output ports（腳位） | 沒有 ports（最外圍封閉的虛擬測試環境）|

### Testbench 基本語法
1. 時間單位與精度 (``timescale`)
* 放在檔案最開頭，用來指定 # 代表多少時間
```verilog
`timescale 1ns / 1ps  
// 前者 1ns 表示單位（#10 代表 10ns）
// 後者 1ps 表示模擬精度（可精確到 0.001ns）
```

2. 模組宣告（無 Input / Output 腳位）
* Testbench 是一個封閉的虛擬測試環境，所以不需要定義 Port 腳位
```verilog
module tb_example();  /
    // 裡面存放訊號與測試邏輯
endmodule
```

3. 訊號型態宣告 (reg 與 wire)
* 對應你要測試的主電路（UUT, Unit Under Test）：
  * reg：用來連接主電路的 input（由 Testbench 賦值與控制）
  * wire：用來連接主電路的 output（接收主電路輸出的結果，用於觀察波形）
```verilog
reg        clk;     // 輸入給主電路的時脈
reg        rst_n;   // 輸入給主電路的重置
reg  [7:0] data_in; // 輸入給主電路的資料

wire [7:0] data_out;// 接收主電路的輸出
```

4. 實例化主電路 (Instantiation)
```verilog
// 格式：主電路名稱 實體名稱 (.主電路腳位(Testbench訊號))
my_design uut (
    .clk     (clk),
    .rst_n   (rst_n),
    .in_a    (data_in),
    .out_b   (data_out)
);
```

5. 時脈 (Clock) 生成
* 模擬器裡沒有實體晶片的震盪器，必須自己寫邏輯來產生 clk
```verilog
// 方式 A：使用 initial 與 forever（最常見）
initial begin
    clk = 0;
    forever #10 clk = ~clk; // 每 10ns 翻轉一次，產生 50MHz 的時脈（週期 20ns）
end

// 方式 B：使用 always
initial clk = 0;
always #10 clk = ~clk;
```

6. 測試流程控制 (initial 區塊與 # 延遲)
* initial 裡面的程式碼只會從第 0 秒開始執行一次，並且由上到下依序執行。# 代表等待的時間
```verilog
initial begin
    // 1. 初始化訊號
    clk     = 0;
    rst_n   = 0;  // 觸發低電位重置
    data_in = 8'd0;

    // 2. 等待 50ns 後解除重置
    #50;
    rst_n   = 1;

    // 3. 依序給予測試資料
    #20; data_in = 8'd15;  // 20ns 後把 data_in 改成 15
    #20; data_in = 8'd42;  // 再過 20ns 改成 42
    #100;

    // 4. 結束模擬
    $finish; 
end
```

7. 文字列印與監控系統任務 ($display, $monitor)
* 類似 C 語言的 printf，可以在 Vivado 下方的 Tcl Console 視窗印出文字資訊，方便快速除錯
  * $display：程式執行到該行時只印出一張單點資訊
  * $monitor：只要監控的變數有變化，就會自動印出來（常用於即時追蹤）
```verilog
initial begin
    // 當 data_in 或 data_out 發生改變時，自動印出時間與數值
    $monitor("Time=%0t ns | in=%d | out=%d", $time, data_in, data_out);
end

initial begin
    #100;
    $display("模擬結束！當前 data_out 為：%d", data_out);
end
```

8. 自動檢查輸出 (if - else 與 $fatal)
* 可以寫邏輯讓 Testbench 自己比對輸出是否正確，省去親自看波形的麻煩。
```verilog
#20;
if (data_out !== 8'd57) begin
    $display("錯誤：計算結果不符合預期！");
    $finish; // 或者使用 $fatal; 強制終止模擬
end else begin
    $display("正確！結果符合 57");
end
```

[回目錄](#toc)

---

<a id="m07d24"></a>

## 2026 年 7 月 24 日

## 今日進度：
### 影片：[財經村長 - 如何面試上一線數位IC公司 II](https://www.youtube.com/watch?v=Xo0VADH-yX4&t=1448s)

## 關鍵知識/詞彙：
### 亞穩態深度解析
<img width="347" height="207" alt="image" src="https://github.com/user-attachments/assets/74ab4088-2dd4-44e8-999e-9b247d2b1e2c" />

1. 為何會發生：
   * 對 DFF 而言，clock edge 附近有兩個限制：
     * Setup time：clock edge 前資料要先穩定一段時間
     * Hold time：clock edge 後資料還要再穩定一段時間
   * 如果 D 在這個視窗內跳動（setup/hold violation），FF 內部的兩個反相器回授會被推到一個「平衡點」附近，進入亞穩態，Q 輸出無法預測。

2. 最常見的發生場景：
   * Clock Domain Crossing（CDC）：非同步訊號或另一個 clock domain 的訊號，進入本 domain 直接被 FF 取樣，最易觸發亞穩態。
   * 非同步 Reset/Interrupt 解除：reset deassert 或外部中斷若沒有同步處理，也會在 clock edge 附近發生問題。

3.解決方案：
* 單 bit：2-FF Synchronizer（最常見）
   *用兩個站存器讓 Data 有時間充放電，接收較穩定的資料，適用場景 1bit
   * 把非同步訊號先送進兩級 FF（同一個 clock）：async_in -> FF1 -> FF2 -> sync_out。FF1 可能亞穩，但大多在下一拍前收斂；FF2 取到的就穩很多。
   * 設計簡單、overhead 低，是標準做法。（降頻、將兩邊頻率改為倍數關係...）
* 多 bit 資料：需更完整機制
   * 多 bit 不能只用 2-FF，需要：握手協議（valid/ready、req/ack）、Async FIFO（含 Gray code 指標）、或 source-synchronous 架構，確保資料整體一致性
* 硬體選型與時序餘裕
   * 選用 library 中 metastability-hardened flop、降頻、改善 clock 品質、降低抖動、增加 slack，從根本降低亞穩態發生機率（以 MTBF 指標衡量）。

### CDC : Clock Domain Crossing 跨時鐘域
1. 簡介：
   * 指一個訊號或資料從 Clock A 的邏輯，跑到 Clock B 的邏輯，而且 A、B 的時鐘彼此不同步（頻率不同或相位不固定）。因為取樣點不受控，容易引發多種嚴重問題。

2. 狀況類型
   * 亞穩態（Metastability）
     * B 域的 FF 在某個 clock edge 取樣到「正在變化」的 A 域訊號，Q 可能晚很久才穩定，導致下一級邏輯誤判。這是 CDC 最根本的危險。
   * 資料撕裂（Multi-bit Tearing）
     * 多位元資料若各 bit 不是同時穩定，B 域可能抓到「一半舊值、一半新值」，形成根本不存在的數值，造成邏輯錯誤。
   * 事件丟失/重複（Pulse Crossing）
     * A 域的一個短脈波，在 B 域可能太窄被漏掉；或因為同步過程被延長而被看成兩次事件，導致功能異常。（快傳至慢：資料遺失；慢傳至快：重複執行）

3. 設計原則
   * 在現代 SoC 設計中，CDC 問題是導致晶片流片失敗的常見原因之一。正確的 CDC 處理策略（同步器、握手、FIFO）必須在架構設計階段就明確規劃，而非事後修補。

### Async FIFO 設計：Gray Code 指標的重要性
<img width="482" height="311" alt="image" src="https://github.com/user-attachments/assets/881d502d-0645-4a13-8445-bd297fb20372" />

1. 簡介
   * 是解決多 bit CDC 問題的標準方案，其核心挑戰在於讀指標（在讀 clock 域）與寫指標（在寫 clock 域）需要跨越 clock domain 比較。

2. 使用 Gray Code 的關鍵原因：
   * Gray Code 相鄰數值只有 1 bit 改變，即使在 CDC 過程中發生亞穩態，最多只影響 1 bit，確保指標比較的正確性。
   * 而 Binary Code 相鄰值可能多個 bit 同時改變，CDC 過程中可能讀到中間過渡值，造成 FIFO full/empty 判斷錯誤。

3. 注意事項
   * Full Flag／Empty Flag產生
   * 深度選擇
     * 需根據兩個 clock 的頻率差與突發資料量計算。預留足夠的 margin 以容納同步延遲（通常 2~3 個 cycle）。

### 低功耗設計（Low Power Design）
1. 時脈閘控技術（Clock Gating）
   * 通常運用在邏輯合成期間。其中的暫存器（flops）被優化成時脈閘控結構，進而節省了多工器（MUX）的面積，並減少整個時脈網路（clock net）的開關活動。
   * 根據動態功率方程式 <img width="188" height="25" alt="image" src="https://github.com/user-attachments/assets/d37f2729-0db1-4434-9320-cd81f2a23d2a" />
   ，時脈閘控的目標是：
     * 降低電容負載（透過面積減少）
     * 減少開關活動因子（關閉不必要的翻轉）
   * 特性與適用場景：
     * 優點：技術簡單、易於實現，大多數 EDA 工具與標準流程均支援，幾乎零風險。
     * 缺點：需依賴邏輯合成工具執行優化，工程師需正確撰寫 RTL 並設定合成條件。
     * 適用：模塊在部分時間不需要運作時，閘控其 clock，避免無效翻轉消耗動態功率。
     * 典型節省：通常可降低整體動態功耗 20%~40%，視設計而異。 

2. 多電壓技術（Multi Voltage / Voltage Islands）
<img width="474" height="317" alt="image" src="https://github.com/user-attachments/assets/35622b49-701c-4446-a534-8f1631b51acd" />

   * 藉由性能特性來區分晶片功能的技術。晶片上某個模塊是高性能的（需要較高電壓以達到速度要求），而其餘部分性能較低，可使用較低電壓運作。
   * 高電壓高頻率的地方使用自己的能耗，低電壓低頻率的地方使用自己的能耗。
   * 根據功率方程式，電壓降低，靜態與動態功耗均降低，因此低性能模塊使用低電壓可顯著節省功耗。
   * 設計複雜度
     * 電壓島（Voltage Islands）：不同電壓域在 layout 上的物理區域劃分
     * Level Shifter（LS）：不同電壓交叉點必須插入電壓位準偏移器，確保訊號電平相容
     * 需要在不同電壓特性下分別分析各模塊的時序、功耗與可靠性
     * UPF（Unified Power Format）或 CPF 格式描述電源意圖

3. 電源閘控技術（Power Gating）
<img width="432" height="318" alt="image" src="https://github.com/user-attachments/assets/8086a184-b5a3-413d-b5cb-ea78cd5c494f" />

   * 如同多電壓技術，晶片上的功能被區分開來，但此技術在功率區域的電源連接了電源開關（Power Switch），可以有效地完全關閉一個模塊的電源。
   * 功率方程式中，將電壓歸零也會使功耗歸零，進而在模塊關閉時同時節省靜態與動態功率，是所有技術中節能效果最佳的。
   * 必要設計元素
     * 電源開關（Power Switch）：控制模塊電源的 ON/OFF
     * 隔離閘（Isolation Gate）：電源關閉時，提供電源區域邊界一個已知狀態（通常為 0 或 1），避免浮接訊號影響其他模塊
     * 電源管理單元（PMU）：控制電源開關與隔離單元的致能訊號，確保斷電與通電有正確的啟動順序
     * 電源狀態表：定義所有電壓 ON/OFF 的狀態組合
   * 當關閉時，module 裡面的每個模組都有自己的 state ，須妥善傳輸與保存（還是須讓下一個module知道狀態）

4. 電源閘控時的保存狀態（State Retention）
<img width="489" height="347" alt="image" src="https://github.com/user-attachments/assets/f4ba1e1e-6ce0-410b-8aa4-69925c1ee50f" />

   * 狀態保存技術（或稱暫存器保存技術，Register Retention）是一種與電源閘控配合使用的技術。在每個關閉的模塊中，當模塊為 OFF 狀態時，模塊中部分或全部的暫存器會保存其原先的數值。當模塊通電時，之前保存的數值就會被恢復。
   * 若不保存狀態，模塊重新上電後必須從 INIT 狀態重新執行，耗費額外的時間與功率。保存狀態可以讓模塊快速恢復至斷電前的運作狀態，大幅縮短喚醒時間，對需要頻繁睡眠/喚醒的應用（如手機 SoC）至關重要。
   * 實現需求：
     * 元件資料庫中需有保存型暫存器（Retention Flop），具備額外的 Shadow Register 儲存保存值
     * PMU 訊號控制序列中需加入 SAVE / RESTORE 訊號
     * 在斷電前執行 SAVE，上電穩定後執行 RESTORE，確保順序正確
     * UPF 中需明確指定哪些 FF 需要 retention 屬性
     * Retention Flop 與普通 Flop 的差異：
       * Retention Flop 內部有一個由獨立低功耗電源供電的 shadow latch，在主電源關閉時保存資料，主電源恢復後再還原。

### Stuck-at Fault 可測性分析
1. 通常為晶片 Tape out 回來之後有些Pattern要打，確定晶片有無問題
2. 測試概念：
   * 要偵測 Stuck-at 1，需要讓正常電路輸出 0，若輸出為 1 則表示有故障。要偵測 Stuck-at 0，則需讓正常輸出為 1。若電路結構上無法讓故障點呈現正確值，即為不可測故障。

[回目錄](#toc)

---

<a id="m07d25"></a>

## 2026 年 7 月 25 日

## 今日進度：
### 影片：
1. [第1講 Vivado設計流程及使用模式](https://www.youtube.com/watch?v=9pylHMr0yfE&t=1s)
2. [How to use Vivado® Design Suite Part-5 Timing Summary Report](https://www.youtube.com/watch?v=zLs8P_PbAV4)
3. [63 - Vivado's Timing Reports](https://www.youtube.com/watch?v=Gdt5IBKGQos&t=254s)
4. [Xilinx Vivado Tutorial: Timing Analysis and Critical Path Optimization](https://www.youtube.com/watch?v=sc8LOViD0Jg&t=317s)

## 關鍵知識/詞彙：
### Vivado 完整流程概念
<img width="264" height="230" alt="image" src="https://github.com/user-attachments/assets/78066304-e249-4fa9-b87c-ba7c289a82c7" />

1. Simulation（模擬）
   * 主要作用： 執行 Behavioral Simulation（行為級模擬）。
   * 搭配你寫好的 Testbench 跑波形圖（Waveform）。在完全不需要連接任何硬體板子的情況下，先確認寫好的 Verilog/VHDL 邏輯功能是否正確。

2. RTL Analysis（RTL 分析 / 電路結構檢視）
   * 主要作用： 展開程式碼的 Schematic（電路原理圖） 與語法檢查。
   * 軟體會初步解析你的程式碼，把它畫成邏輯閘、暫存器（Flip-Flops）與多工器（Mux）組成的架構圖。你可以用它來確認： Vivado 解讀出的電路結構，是否跟自己所想像的一模一樣。
     
3. Synthesis（邏輯綜合）
   * 主要作用： 將文字程式碼轉換為門級網表（Gate-level Netlist）。
   * 軟體會把你寫的抽象邏輯（如 +、 -、 if-else），翻譯成 FPGA 晶片內部實際存在的底層單元，例如 LUT (Look-Up Table)、Flip-Flop (FF) 和 Block RAM。
     
4. Implementation（布局與布線 - Place & Route）
   * 主要作用： 將綜合後的電路實體化，並進行時序分析。
   * Placement (布局)： 決定把 Synthesis 產生的 LUT 和 FF 放到 FPGA 晶片上的哪個具體物理位置。
   * Routing (布線)： 將晶片內部的金屬導線接通。
   * Timing Analysis (時序分析)： 檢查訊號傳輸速度是否太慢、會不會產生 Setup/Hold Time 違規（Timing Violation）。

5. Program and Debug（燒錄與硬體除錯）
   * 主要作用： 生成燒錄檔、連線 FPGA 開發板與在線除錯。
   * Generate Bitstream： 將 Implementation 的結果打包成 .bit 燒錄檔。
   * Hardware Manager： 透過 USB 線連接開發板，把 .bit 檔燒進 FPGA 晶片。
   Logic Analyzer (ILA)： 當板子運作不正常時，可以在晶片內加入抓訊號的儀器，即時回傳晶片內部的波形到 Vivado 畫面上進行除錯。

6. 標準開發流程順序：
   * Simulation (先模擬) -> Synthesis (邏輯綜合) -> Implementation (布局布線) -> Program and Debug (產出 bit 檔並燒錄)

### XDC 檔（Xilinx Design Constraints）
1. 簡介
   * Vivado 的約束檔（Constraint File），副檔名為 .xdc。
   * XDC 檔就是指定「這個器官要連接到 FPGA 晶片的哪一個實體腳位」，以及「系統的時脈頻率是多少」。

2. XDC 檔最常見的兩大功能：
   * Pin Assignment（管腳約束 / 腳位綁定）：指定你的輸入輸出訊號對應到板子上的哪個按鈕或 LED 燈。
     ```verilog
     # 把 LED 訊號綁定到 FPGA 的 Y13 腳位，並設定電壓標準為 LVCMOS33
		set_property PACKAGE_PIN Y13 [get_ports {led}]
		set_property IOSTANDARD LVCMOS33 [get_ports {led}]
     ```
   * Clock Constraint（時脈約束）：告訴 Vivado 你的 Clock 頻率是多少（讓 Vivado 知道怎麼計算 WNS 報告）。
     ```verilog
     # 告訴 Vivado，sys_clk 這個腳位輸入的時脈週期是 10ns (也就是 100MHz)
		create_clock -period 10.000 -name sys_clk [get_ports sys_clk]
     ```
   
### Timing Report（時序報告）
<img width="756" height="125" alt="image" src="https://github.com/user-attachments/assets/b2da2c7b-28ca-4346-b51f-6256f45d8d5e" />

<img width="1358" height="725" alt="image" src="https://github.com/user-attachments/assets/18e836a5-0775-4527-a430-e66fdf442b1d" />


1. 簡介
   * 跑完 Implementation 後，Vivado 會幫你做全面嚴格的「時序檢查」。
   * 就像是電路的「體檢報告」。它告訴你：你的電路傳輸速度，能不能跟上你設定的 Clock（時脈）頻率？

2. 報告中最重要的地方
   * 最核心的就是查看電路有沒有 Timing Violation（時序違規），也就是訊號會不會「來不及跑完」。
   * 標準就是 Slack（時序裕量）：
     * Slack > 0（綠色）： 訊號傳輸時間綽綽有餘，電路可以在預期頻率下完美運作。
     * Slack < 0（紅色）： 訊號傳輸太慢了（Setup Time Violation）或太快了（Hold Time Violation），電路在實體晶片上會出錯（爆 Bug）。

3. WNS（Worst Negative Slack 最壞負裕量）
   * 定義： 全晶片所有路徑中，最嚴重的那條「延遲/卡頓」路徑缺了多少時間。
   * 重點評估：
     * 如果 WNS >= 0 ns：代表整張晶片的時序全部過關（Timing Met）。
     * 如果 WNS = -1.2 ns：代表最慢的那條路徑，超出了規定的時脈週期 1.2 ns，須優化程式碼或降低 Clock 頻率。

### Utilization 報告（資源使用率）
<img width="380" height="205" alt="image" src="https://github.com/user-attachments/assets/3470d57f-b3f7-48f5-b4ce-1e9914a2fe44" />

1. 定義： 你的程式碼佔用了這顆 FPGA 晶片百分之多少的硬體資源。
   
2. 重要性： 觀察晶片會不會「被塞爆」。如果 Utilization 超過 80% ~ 90%，Vivado 的 Implementation 會變得極度困難，甚至會導致時序爆發（WNS 變成負值）。
   
3. LUT / FF 用量

| 資源名稱 | 全名 | 核心功能與作用 |
| :--- | :--- | :--- |
| LUT | Look-Up Table (查找表) | 實現**組合邏輯（Combinational Logic）**。例如加法、減法、`if-else`、`case` 判斷。 |
| FF | Flip-Flop (觸發器/暫存器) | 實現**時序邏輯（Sequential Logic）**。用來儲存 1 bit 的資料，必須靠 Clock 驅動。 |

### 實際開發的連鎖反應
1. Timing Report、Utilization兩個報告要「一起看」
   * 如果 Utilization 報告顯示 LUT / FF 用量超過 80%~90%，晶片裡面會變得非常擁擠。Vivado 在做 Implementation時，被迫要把相連的邏輯放到距離很遠的地方，繞很長的金屬線。會導致訊號傳輸延遲大幅增加，最後在 Timing Report 裡跳出紅色的 WNS < 0（時序違規）。

[回目錄](#toc)

---

<a id="m07d27"></a>

## 2026 年 7 月 27 日

## 今日進度：
### 資料：
1. [Barrel Shifters in Verilog: A Beginner’s Guide to Fast Multi-Bit Shifting](https://medium.com/@ahe24mobile/barrel-shifters-in-verilog-a-beginners-guide-to-fast-multi-bit-shifting-121d1c5a2b62)
2. [How to Design an Efficient Barrel Shifter in Verilog: Step-by-Step Guide](https://vlsifacts.com/how-to-design-an-efficient-barrel-shifter-in-verilog-step-by-step-guide/)

## 今日成果探討：
### 32bit_ALU_V1（Baseline）- 設計初步 32-bit 構造、模型以及所支援的運算
<img width="544" height="316" alt="image" src="https://github.com/user-attachments/assets/edfdfcaf-8039-4a88-8e76-640d7a6a100d" />
<img width="923" height="507" alt="image" src="https://github.com/user-attachments/assets/24a57abe-c425-42a0-8ac1-af8253aafec1" />

### 8-bit Logical Left Barrel Shifter
* Design sources
```verilog
module Logical_Left_Barrel_Shifter(

    input [7:0]d_in,
    input [2:0]shamt,
    output [7:0]d_out
    
 );
    
    wire [7:0] t1, t2;
    
    // Shift by 1 bit if shamt第 0 位 == 1
    assign t1 = shamt[0] ? {d_in[6:0], 1'b0} : d_in;
    // Shift by 2 bit if shamt第 1 位 == 1
    assign t2 = shamt[1] ? {t1[5:0], 2'b00} : t1;
    // Shift by 4 bit if shamt第 2 位 == 1
    assign d_out = shamt[2] ? {t2[3:0], 4'b0000} : t2;
    
endmodule
```

* Simulation sources
```verilog
module barrel_shifter_tt();

reg [7:0]d_in;
reg [2:0]shamt;
wire [7:0]d_out;

Logical_Left_Barrel_Shifter tt(
    .d_in(d_in),
    .shamt(shamt),
    .d_out(d_out)
);

initial begin
    d_in = 8'b10110011; // Initialize input data 
    // Test all shift amounts from 0 to 7
    for(shamt=0; shamt<8; shamt=shamt+1)begin
        #10;// Wait 10 time units for output to stabilize
        $display("Time=%0t | shift_amt=%0d | data_in=%b | data_out=%b"
        ,$time, shamt, d_in, d_out);
    end
    #10;
    $finish;
end
```

* 模擬結果
<img width="536" height="377" alt="image" src="https://github.com/user-attachments/assets/3b93234d-1639-46c6-b798-0b28319c5f0f" />
<img width="512" height="159" alt="image" src="https://github.com/user-attachments/assets/1bba3fc9-40d6-4fe6-8058-f67cada8dd00" />



## 關鍵知識/詞彙：
### 邏輯移位／算數移位
1. 左移與右移的核心差別
   * 數學語意上的不同（乘法 vs 除法）：
     * 左移（Left Shift）： 位元向高位移動，數值上等同於乘以 2^n
     * 右移（Right Shift）： 位元向低位移動，數值上等同於除以 2^n（自動向下取整）

   * 補位規則的對稱性：
     * 左移（<< / <<<）： 補位行為完全對稱且一致，無論是邏輯還是算術，右側（LSB）空出來的位置一律補 0
     * 右移（>> / >>>）： 是算術與邏輯差異最顯著之處。邏輯右移左側永遠補 0；算術右移（且變數為 signed）左側會補最高有效位（MSB，即正數補 0、負數補 1），用以保持負數的數值正確性
  
   * 邊界溢位與精度損失：
     * 左移容易造成高位溢位（Overflow），若接收變數的位元寬度不夠，最左側的位元將會被丟棄
     * 右移則會造成低位精度的捨棄（Truncation），最右側移出的位元會直接丟失（相當於整數除法捨去餘數）。

2. 邏輯移位 vs 算術移位比較表

| 比較項目 | 邏輯移位 (Logical Shift) | 算術移位 (Arithmetic Shift) |
| :--- | :--- | :--- |
| **語法運算子** | `<<`（左移）、`>>`（右移） | `<<<`（左移）、`>>>`（右移） |
| **符號屬性 (Signedness)** | 強制作為 **Unsigned（無符號）** 處理 | 保留 **Signed（有符號）** 屬性<br>*(須宣告為 `signed` 變數)* |
| **左移補位行為** | 右側永遠**補 0** | 右側永遠**補 0**（與邏輯左移相同） |
| **右移補位行為** | 左側（MSB）永遠**補 0** | 若變數為 signed 則**補符號位 (MSB)**<br>若變數為 unsigned 則**補 0** |
| **位元擴展行為<br>(Sign Extension)** | 賦值給較長位元時，高位直接**補 0** | 賦值給較長位元時，依據符號位進行**符號擴展** |
| **主要應用場景** | 資料封包拆解、Mask 遮罩、純位元操作 | 有符號數的數學快速乘除法（2 的次方的乘除） |

### 桶型移位器 (Barrel Shifter) 
1. 簡介：是一種純組合邏輯電路，其最大特點為「可以在單一時脈週期內將資料向左或向右移動任意位數」

2. 運作原理：內部通常由多級 Multiplexer（MUX）對角線式連接而成。以 8-bit Barrel Shifter 為例，內部設計為 3 級 MUX：
   * 第 1 級決定是否移位 1 位 (2^0)
   * 第 2 級決定是否移位 2 位 (2^1)
   * 第 3 級決定是否移位 4 位 (2^2)
   * 藉由這些 2 的次方組合，即可在極短的組合邏輯延遲（O(\log N)）內完成 0~7 位的任意移位。

3. 適用情境：廣泛應用於現代 CPU 的 ALU（算術邏輯單元）與 DSP 中，用來支援單指令週期的位移操作。

### 一般移位暫存器 (Standard Shift Register)
1. 簡介：是一種時序邏輯電路，由多個 Flip-Flop（觸發器）串聯而成。

2. 運作原理：在每一個時脈邊緣（Clock Edge）到來時，資料會從前一級 Flip-Flop 傳遞到下一級，實現「每次 Clock 只移動 1 位元」的行為。
   * 優缺點： 雖然若要移位 N 位元需要花費 N 個時脈週期（吞吐量較低），但其電路結構非常簡潔、佔用晶片面積小，且沒有複雜的組合邏輯延遲問題。

3. 適用情境：適合用於串列通訊資料轉換（如 SPI、UART、I2C 的 SIPO / PISO 轉換）或暫存佇列（Shift Register FIFO）。

### Barrel Shifter vs 一般移位暫存器比較表

| 比較項目 | Barrel Shifter（桶型移位器） | 一般移位暫存器 (Standard Shift Register) |
| :--- | :--- | :--- |
| **核心架構** | 多級多工器（Multiplexer Array）組合邏輯 | 觸發器（D Flip-Flop）級聯串列時序電路 |
| **電路類型** | **純組合邏輯 (Combinational Logic)** | **時序邏輯 (Sequential Logic)** |
| **移位時間 (延遲)** | **1 個時脈週期內 (1 Clock Cycle)** 完成任意 $N$ 位元移位（延遲為 $O(\log N)$） | 需要 **$N$ 個時脈週期 (N Clock Cycles)** 才能移位 $N$ 位元（延遲為 $O(N)$） |
| **硬體資源 (面積)** | 較高，隨位元數成 $O(N \log N)$ 成長（大量 MUX） | 較低，隨位元數成 $O(N)$ 成長（僅需 DFF 與少許邏輯） |
| **關鍵路徑/延遲** | 組合邏輯線路較長（Gate Delay 較大），容易成為 Critical Path | 每個 Cycle 延遲極小（僅 DFF 的 $T_{co}$），易於達成高 Clock Frequency |
| **移位位數彈性** | 任意位數可單一週期直接指定（如直接移 5 位） | 逐位移位，移幾位就需要幾個 Clock 觸發 |
| **主要應用場景** | ALU、浮點數運算單元 (FPU)、DSP 算術運算、處理器指令集 (ARM Shifter) | 串列/並列轉換 (SIPO/PISO)、通訊介面 (SPI/UART)、資料緩衝佇列 |

* Trade-off when using Barrel Shifter：需要更多的邏輯電路（例如，a tree of multiplexers），從而換取更高的速度。當需要在一個週期內進行變速時，這種方法是值得的。

[回目錄](#toc)

---

<a id="m07d28"></a>

## 2026 年 7 月 28 日

## 今日進度：
### 資料：
1. [Verilog code for Arithmetic Logic Unit (ALU)](https://www.fpga4student.com/2017/06/Verilog-code-for-ALU.html)
2. [ALU design in Verilog using MIPS Instruction Set](https://electrobinary.blogspot.com/2021/02/alu-design-in-verilog-using-mips.html)

## 今日成果探討：
### ALU 設計：
### 32bit_ALU_V1（Baseline）- 完成 32-bit ALU baseline 版本與 debug 
* Design sources
```verilog
module alu_v1(
    input [31:0]a,
    input [31:0]b,
    input [2:0]op_code,
    output reg[31:0]res,
    output [3:0]flag
);
    //構建4個 32bit 暫存器用於 barrel shifter
    //Shamt 選用 b[4:0] 判斷移動幾個 bit
    reg [31:0]t1, t2, t3, t4;
    
    // 構建共用的 33-bit 加減法邏輯（在 case 外面算）
    wire sub;
    wire [31:0]b_op;
    wire [32:0]add_sub;
    wire carry;
    wire overflow;

    assign sub = (op_code == 3'b001);// op_code == 3'b001，執行減法
    assign b_op = b ^ {32{sub}}; // 將 b 逐位元反相
    assign add_sub = {1'b0, a} + {1'b0, b_op} + sub; // ADD: a+b；SUB: a + ~b + 1（2補數，+1 由 sub 提供）
    assign carry = add_sub[32]; //進位判斷 
    assign overflow = (~(a[31] ^ b_op[31])) && (a[31] ^ add_sub[31]);//溢位判斷

// 判斷 ALU 行為
always@(*)begin
    case(op_code)
        3'b000 : // a+b
            res = add_sub[31:0]; 
        3'b001 : // a-b 
            res = add_sub[31:0];
        3'b010 : 
            res = a & b;
        3'b011 : 
            res = a | b;
        3'b100 : 
            res = a ^ b;
        3'b101 : begin // 邏輯左移(<<)，缺項補0
            t1 = (b[0]) ? {a[30:0], 1'b0} : a;
            t2 = (b[1]) ? {t1[29:0], 2'b00} : t1;
            t3 = (b[2]) ? {t2[27:0], 4'b0000} : t2;
            t4 = (b[3]) ? {t3[23:0], 8'b00000000} : t3;
            res = (b[4]) ? {t4[15:0], 16'b00000000} : t4;
        end 
        3'b110 : begin // 算術右移(>>>)，補 signed bit a[31]
            t1 = (b[0]) ? {a[31], a[31:1]} : a;
            t2 = (b[1]) ? {{2{a[31]}}, t1[31:2]} : t1;
            t3 = (b[2]) ? {{4{a[31]}}, t2[31:4]} : t2;
            t4 = (b[3]) ? {{8{a[31]}}, t3[31:8]} : t3;
            res = (b[4]) ? {{16{a[31]}}, t4[31:16]} : t4; 
        end
        3'b111 : res = ($signed(a) < $signed(b)) ? 32'd1 : 32'd0; // 有號數比較
        default : res = 32'd0;
    endcase
end

// 構建 flag
wire a_s;
wire Z, N, C, V;

// flag 只在 ADD/SUB 時才有意義
assign a_s = (op_code == 3'b000) || (op_code == 3'b001);

assign Z = (res == 32'd0) ? 1 : 0; // 零旗標判斷
assign N = (res[31]) ? 1 : 0; // 負旗標判斷
assign C = (a_s) ? carry : 0; // 進位旗標判斷
assign V = (a_s) ? overflow : 0; // 溢位旗標判斷
    
assign flag = {Z, N, C, V}; // flag[3]=Z, flag[2]=N, flag[1]=C, flag[0]=V

endmodule
```

* Simulation sources
```verilog
module alu_v1_tt();

    reg [31:0]a, b;
    reg [2:0]op_code;
    wire [31:0]res;
    wire [3:0]flag;
    integer i;
    
    alu_v1 tt(
    .a(a),
    .b(b),
    .op_code(op_code),
    .res(res),
    .flag(flag)
    );
    
    initial begin
    $monitor("time=%0t op_code=%b a=%h b=%h res=%h flag=%b", $time, op_code, a, b, res, flag);
    a = 32'hF150A1B8;
    b = 32'h5F58258;
    for (i = 0; i < 8; i = i + 1) begin
        op_code = i[2:0];   // 對應 000~111
        #10;
    end
    $finish;   
    end   
endmodule
```

* 模擬結果
<img width="745" height="377" alt="image" src="https://github.com/user-attachments/assets/28336d72-38ed-4462-ac23-e0a8f90fb22c" />

## 遇到的困難與解決方案：
### 問題1：SLT 比較負數時判斷結果錯誤
* 原因：Verilog 的 wire/reg 預設是 unsigned，`a < b` 這種比較不會考慮 2 補數的符號位，導致負數被當成很大的正數去比大小
* 解法：用 `$signed()` 這個系統函式，把 `a`、`b` 重新解讀成有號數再比較
* 程式碼：
  ```verilog
	res = ($signed(a) < $signed(b)) ? 32'd1 : 32'd0;
  ```

### 問題2：一開始搞混了 signed() 跟 ~b+1 的用途，以為 a + $signed(b) 可以拿來做減法
* 原因：誤以為 `$signed()` 會「改變」訊號的數值
* 釐清後的觀念：
  * `$signed()` 是**重新解讀（reinterpretation）**：不會動任何一個 bit，只是告訴後面的運算子「把最高位當符號位去解讀」，本質上像加一個濾鏡去看同一份資料
  * `~b + 1` 是**數值轉換（value transformation）**：把每個 bit 反相再加 1，算出一個新的數值（2 補數的 -b）
  * 減法需要的是「真的把 b 變成 -b」，屬於數值轉換，`$signed()` 做不到這件事
* 另外發現：想共用加法器做減法、還要順便算 carry/overflow 時，要把 `a`、`b` 都多補一位變成 33-bit 再相加，「多留一個 bit 裝進位」的動作，`$signed()` 也做不到
* 程式碼：
  ```verilog
	// 構建共用的 33-bit 加減法邏輯（在 case 外面算）
    wire sub;
    wire [31:0]b_op;
    wire [32:0]add_sub;
    wire carry;
    wire overflow;

    assign sub = (op_code == 3'b001);// op_code == 3'b001，執行減法
    assign b_op = b ^ {32{sub}}; // 將 b 逐位元反相
    assign add_sub = {1'b0, a} + {1'b0, b_op} + sub; // ADD: a+b；SUB: a + ~b + 1（2補數，+1 由 sub 提供）
    assign carry = add_sub[32]; //進位判斷 
    assign overflow = (~(a[31] ^ b_op[31])) && (a[31] ^ add_sub[31]);//溢位判斷
  ```

## 關鍵知識/詞彙：
### $signed()
* Verilog 系統函式，將訊號重新解讀為 2 補數表示的有號數，只改變「詮釋方式」，不改變原始 bit pattern
* 只在該次運算當下生效，不影響同一訊號在其他運算中的解讀方式

### Part Select（位元選取）
* 語法：訊號[高位:低位]，從一個多位元的訊號裡，取出連續一段 bit
* 範例：integer i 是 32-bit 有號數，i[2:0] 代表只取出 i 最低的 3 個 bit（第2、1、0位）
* 用途：當來源訊號位寬比目標訊號寬時，可以用 part select 明確指定要取哪幾位，讓程式碼意圖更清楚

```verilog
integer i;          // 32-bit
reg [2:0] op_code;   // 3-bit

op_code = i[2:0];    // 明確取 i 的最低 3 位給 op_code
```

* 補充：如果直接寫 op_code = i;（不加 part select），Verilog 會自動做**隱含截斷（implicit truncation）**——把較寬的訊號指派給較窄的訊號時，自動只取最低的幾個 bit、捨棄多出來的高位，結果跟 i[2:0] 完全一樣
* 那為什麼寫 i[2:0]：雖然結果一樣，但明寫出來能讓看程式碼的人清楚知道「這是刻意只取低位，不是漏寫」

### 相關語法：Part select 也能用在賦值的左邊
* 不只能「讀取」部分位元，也能只「賦值」給訊號的一部分，例如：
```verilog
reg [7:0] data;
data[3:0] = 4'b1010;   // 只改 data 的低 4 位，高 4 位不受影響
```

[回目錄](#toc)

---

<a id="m07d30"></a>

## 2026 年 7 月 30 日

## 今日進度：
### 刷題：複習 HDLBits 7/3 - 7/17 進度。
### 影片：
1. [A Practical Introduction to Edge AI](https://youtu.be/ibm6ZRi6Sm4?si=yXesCNbBqkw3CrrX)
2. [FPGA 是怎麼被發明的? 一口氣了解 Xilinx 跟 Altera 的 FPGA 爭霸史](https://www.youtube.com/watch?v=kRSLnkOKpPY)
3. [CACM September 2018 - A Domain Specific Architecture for Deep Neural Networks](https://www.youtube.com/watch?v=MbO_pIQLP34)
4. [A Practical Introduction to Edge AI](https://youtu.be/ibm6ZRi6Sm4?si=yXesCNbBqkw3CrrX)

## 關鍵知識/詞彙：
### DSA（Domain Specific Architecture，特定領域架構）
1. 定義：不追求「什麼都能做」的通用處理器（CPU），而是**針對特定任務**（例如矩陣運算、訊號處理、AI 推論）設計專用硬體電路，**犧牲通用性換取效能與能耗效率**。

2. 背景：
	* 摩爾定律放緩，單靠製程微縮已難以大幅提升效能
	* 通用 CPU 為了「什麼都能做」，硬體資源花在指令解碼、分支預測等通用邏輯上，效率不高
	* 特定應用（尤其是深度學習）的運算模式很固定（大量矩陣乘加），適合客製化硬體加速

3. 代表性例子：
	* Google TPU（針對深度學習矩陣運算設計）
	* GPU（針對平行運算優化，通用性介於 CPU 與 DSA 之間）
	* 各種 AI 加速器 IP（NPU）

4. 效能/彈性光譜：
	* CPU（通用）-> GPU（平行）-> DSA/ASIC（專用）
	* 客製化程度越高，效能/能耗比越好，但彈性越差、開發成本越高。

### Edge AI（邊緣運算 AI）
1. 定義：把 AI 推論（inference）放在終端裝置（手機、IoT 裝置、感測器）上執行，而不是把資料傳到雲端伺服器算完再傳回來。
   
2. 優點：
	* 延遲：**本地運算完成**，不需等待網路來回
	* 隱私：資料**不需上傳雲端**
	* 頻寬/成本：不需持續傳輸大量資料
	* 可靠性：**無網路連線**時仍可運作

3. 與 IC 設計的關聯：
	* Edge 裝置多為電池供電、體積受限，因此需要**低功耗、小面積**的 AI 加速器，而非雲端伺服器那種高功耗換高效能的做法
	* 邊緣裝置上的 AI 晶片，是 DSA 概念的實際應用之一：為推論任務特化，同時兼顧功耗與面積
	* 常見技術方向：
  		* 模型量化（以 INT8 甚至更低位元取代浮點運算，節省功耗與面積）
  		* 記憶體存取優化（AI 運算瓶頸常在資料搬移而非運算本身，因此有 In-memory computing 等討論方向）

[回目錄](#toc)

---

<a id="m07d31"></a>

## 2026 年 7 月 31 日

## 今日進度：
### 資料：
1. 複習 7/3 - 7/30 進度
2. [MIT 6.111 課程講義 Lecture 9《Pipelining & Verilog》(PDF)](https://web.mit.edu/6.111/www/f2016/handouts/L09.pdf)

## 關鍵知識/詞彙：
### Pipeline（管線化 / 流水線）
* 一種資料路徑設計技巧，把一段長的組合邏輯切成幾個較短的階段，中間插入暫存器，讓每個階段各自在一個 clock cycle 內完成
* 核心目的：**不是縮短單一筆資料的處理時間，而是提升整體吞吐量（throughput）**，因為每個階段變短了，可以用更高的 clock frequency 運作，而且每個 clock cycle 都能開始處理新的一筆資料

### Latency vs Throughput
* **Latency（延遲）**：一筆資料從輸入到輸出總共花的時間。切了 pipeline 之後，因為多了暫存器，latency 通常會「增加」
* **Throughput（吞吐量）**：單位時間內能處理完幾筆資料。切了 pipeline 之後，滿水位後可以每個 cycle 就出一筆結果，throughput 會明顯提升
* pipeline 設計的核心 **trade-off**：**犧牲一點 latency，換取大幅提升的 throughput**

### 為什麼切 Pipeline 能讓頻率變高
* 電路能跑多快，取決於整條電路裡最長的那條路徑（critical path）
* 例如一個 32-bit RCA 加法器的 critical path 是進位一路傳到最高位的延遲；如果從中間插一個暫存器切成兩段（低16/高16），critical path 就變成只需要走完「半段」的延遲，自然能撐更高的 clock frequency
* 換句話說：pipeline 是用「空間換時間」——多花面積（暫存器）去換取更短的每級延遲

### Balanced Pipeline（平衡管線）
* 切 pipeline 時，如果某條路徑（例如邏輯運算）本身延遲很短，也要跟著切同樣深度的暫存器級數，確保所有分支的資料在「同一拍」到齊
* 常見的錯誤：只把最長的那條路徑（如加法器）切了暫存器，其他較短的路徑忘記跟著切，導致 MUX 選出來的資料其實不是同一時間點的結果，造成功能錯誤

### Pipeline 在單顆 ALU（無資料相依）跟 CPU pipeline 的差別
* 單獨一顆 ALU 電路本身是無狀態（stateless）的組合功能單元，插入 pipeline 不會有 data hazard 問題
* CPU 的五級 pipeline（IF/ID/EX/MEM/WB）裡，hazard 問題來自「指令之間」的相依性（例如下一條指令要用到上一條還沒算完的結果），跟切 ALU 內部的 pipeline 是完全不同層次的問題

[回目錄](#toc)

---

<a id="m08d01"></a>

## 2026 年 8 月 1 日

## 今日成果探討：
### ALU 設計：
### 32bit_ALU_V1（Baseline） - 改寫 32-bit ALU baseline 版本 testbench
* 這次測試設計採用 corner case（邊界案例）驗證思路，針對電路裡最容易出錯的幾個地方各自設計專門的測資，而不是隨機測試。
* golden value（預期結果）全部自己手動用二進位/2補數算過，沒有拿 RTL 邏輯反推，避免用同一套可能有 bug 的邏輯驗證自己。

1. ADD / SUB 基本功能
```verilog
check(32'h0000_00F0, 32'h0000_000F, 3'b000, 32'h0000_00FF, 0,0,0,0); // ADD
check(32'h0000_00F0, 32'h0000_000F, 3'b001, 32'h0000_00E1, 0,0,1,0); // SUB
```
* 用意：先確認共用的 33-bit 加減法邏輯（`add_sub`）在正常數值下功能正確，這是後面所有 flag 判斷的基礎，要先確保這塊沒問題
* SUB 那組刻意讓 `a > b`，驗證正常減法沒有借位時 carry 應該是 1（`C=1`）

2. AND / OR / XOR 邏輯運算覆蓋率
```verilog
check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b010, 32'h0F00_0F00, 0,0,0,0); // AND
check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b011, 32'hFF0F_FF0F, 0,1,0,0); // OR
check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b100, 32'hF00F_F00F, 0,1,0,0); // XOR
```
* 用意：一開始這 3 個運算完全沒被測到，屬於覆蓋率破洞，補上確保 8 個 opcode 全部至少測過一次
* 選用 `FF00FF00` / `0F0F0F0F` 這種棋盤式 bit pattern，方便手動驗算每個 bit 的邏輯結果，也能同時檢查出每組結果的正負號（N flag）算得對不對

3. SRA 符號延伸（sign extension）
```verilog
check(32'hF123_4567, 32'h0000_0004, 3'b110, 32'hFF12_3456, 0,1,0,0); // SRA
```
* 用意：驗證算術右移補的是「符號位」而不是固定補 0。刻意選 `a` 最高位是 1（負數）的數值，因為如果 `a` 是正數，SRA 補符號位跟 SRL 補0結果會一樣，根本測不出補位邏輯有沒有寫對
* `shamt = b[4:0] = 4`，也順便驗證了移位量不是固定值、而是真的照 `b` 的低5位在算

4. ADD Overflow 邊界
```verilog
check(32'h7FFF_FFFF, 32'h0000_0001, 3'b000, 32'h8000_0000, 0,1,0,1); // ADD overflow
```
* 用意：`0x7FFFFFFF` 是 32-bit 有號數能表示的最大正數，加 1 之後理論上會變成 `0x80000000`（有號數解讀下是最負的數），這是刻意設計來觸發 overflow 的邊界案例
* 專門驗證 overflow 判斷式 `(~(a^b_op)) && (a^add_sub)`（兩運算元同號、結果卻異號）有沒有正確抓到這種情況，一般隨便挑的數值很難自然踩到這個邊界

5. SLT 有號數比較
```verilog
check(32'hFFFF_FFFF, 32'h0000_0005, 3'b111, 32'h0000_0001, 0,0,0,0); // SLT
```
* 用意：`0xFFFFFFFF` 當 unsigned 看是一個很大的正數，當 signed 看則是 -1。這組刻意用這個數值，就是要驗證 `$signed(a) < $signed(b)` 有沒有真的把它當成負數處理
* 如果沒加 `$signed`，這組測試會判斷成「4294967295 < 5 為假」，跟正確答案（-1 < 5 為真）相反，是專門抓「忘記處理 signed」這種 bug 的案例

6. Shamt 邊界值（0 與 31）
```verilog
check(32'h0000_0001, 32'h0000_0000, 3'b101, 32'h0000_0001, 0,0,0,0); // shamt=0
check(32'h0000_0001, 32'h0000_001F, 3'b101, 32'h8000_0000, 0,1,0,0); // shamt=31
```
* 用意：移位量的合法範圍是 0~31，這是兩端的極值，最容易被忽略（很多人只測中間值）
* `shamt=0`：驗證「不移位」的情況下，barrel shifter 每一級的 mux 都要正確選到「不動」那個分支，結果應該跟輸入完全一樣
* `shamt=31`：驗證移到底的情況，5 級 mux 全部要選到「移」的那個分支，結果應該只剩最低位的 1 被推到最高位

* Simulation sources
```verilog
module alu_v1_tt();

    reg [31:0]a, b;
    reg [2:0]op_code;
    wire [31:0]res;
    wire [3:0]flag;
    integer i;
    
    integer error_count; // 用於計算"error"次數
    integer test_count; // 用於計算"test"次數
    
    alu_v1 tt(
    .a(a),
    .b(b),
    .op_code(op_code),
    .res(res),
    .flag(flag)
    );
    
    task automatic check(
        input [31:0]t_a, // a的測試變數
        input [31:0]t_b, // b的測試變數
        input [2:0]t_op, // op_code的測試變數
        input [31:0]t_res, // res的測試變數
        input t_z, t_n, t_c, t_v // flag的測試變數
    );
        begin
            a = t_a; b = t_b; op_code = t_op;
            #10;
            test_count = test_count + 1; // 每次開始測試 test_count 次數 +1
            if(res !== t_res || flag !== {t_z, t_n, t_c, t_v})begin // res 結果或 flag 結果不符
                error_count = error_count + 1; // 錯誤就把 error_count 次數 +1
                $display("[FAIL]：%0d, op_code = %b, a = %h, b = %h | got res = %h, flag = %b | t_res = %h, flag = %b",
                test_count, t_op, t_a, t_b, res, flag, t_res, {t_z, t_n, t_c, t_v});
            end
            else begin
                $display("[PASS]：%0d, op_code = %b", test_count, t_op);
            end
        end
    endtask
    
    initial begin
        error_count = 0;
        test_count = 0;
        
        // 基本功能：ADD / SUB 
        check(32'h0000_00F0, 32'h0000_000F, 3'b000, 32'h0000_00FF, 0,0,0,0); // ADD
        check(32'h0000_00F0, 32'h0000_000F, 3'b001, 32'h0000_00E1, 0,0,1,0); // SUB
        
        // 邏輯運算：AND / OR / XOR
        check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b010, 32'h0F00_0F00, 0,0,0,0); // AND
        check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b011, 32'hFF0F_FF0F, 0,1,0,0); // OR
        check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b100, 32'hF00F_F00F, 0,1,0,0); // XOR
        
        // 針對電路容易出錯的地方各自設計測資
        check(32'hF123_4567, 32'h0000_0004, 3'b110, 32'hFF12_3456, 0,1,0,0); // SRA 符號延伸
        check(32'h7FFF_FFFF, 32'h0000_0001, 3'b000, 32'h8000_0000, 0,1,0,1); // ADD overflow
        check(32'hFFFF_FFFF, 32'h0000_0005, 3'b111, 32'h0000_0001, 0,0,0,0); // SLT：-1 < 5
        check(32'h0000_0001, 32'h0000_0000, 3'b101, 32'h0000_0001, 0,0,0,0); // shamt=0
        check(32'h0000_0001, 32'h0000_001F, 3'b101, 32'h8000_0000, 0,1,0,0); // shamt=31
        
        $display("\n測試完成：共 %0d 組，失敗 %0d 組", test_count, error_count);
        if(error_count == 0)begin
            $display("ALL TESTS PASSED");
        end
        $finish;       
    end  
endmodule
```

* 模擬結果
<img width="214" height="251" alt="image" src="https://github.com/user-attachments/assets/806079cd-4cc0-4a13-b4b7-5297b3a1180a" />

## 遇到的困難與解決方案：
### 問題：跑 alu_tt 模擬沒有跑出結果
* 原因：task 內少寫 `#10`，比對時機在 DUT 組合邏輯還沒重新算完前就執行，導致比對到殘留的舊值（race condition）
* 解法：修法是每次改完輸入後一定要留一段延遲再比對

## 關鍵知識/詞彙：
### task
* 用來把一段重複會用到的程序邏輯包裝起來的語法，概念類似其他語言的"函式（function）"，但更專門用於 procedural/testbench 情境
* 語法結構：
```verilog
task automatic 任務名稱(
    input  型別 參數1,
    input  型別 參數2,
    output 型別 參數3   // 也可以有輸出參數
);
    begin
        // 任務內容
    end
endtask
```
* 呼叫方式跟函式一樣：`任務名稱(引數1, 引數2, ...);`

### task vs function 的差別
* `function` 一定要回傳一個值（像數學函式 y=f(x)），且**不能用延遲語法（#10 這種）**
* `task` 不用回傳值，**可以在裡面使用延遲語法**，因為驗證流程常需要「設定輸入 -> 等待一段時間 -> 再比對結果」，所以 testbench 裡通常用 task 而不是 function

### automatic 關鍵字
* 代表這個 task 每次被呼叫時，會重新配置一份獨立的變數空間（可重入 re-entrant）
* 如果不加（預設是 static），task 裡的變數是全域共用同一份，多處同時呼叫同一個 task 時容易互相干擾出錯
* 寫 testbench 時養成習慣加 automatic 比較保險

### task 裡的 input 跟 module port 的 input 不是同一件事
* module 的 input 是硬體接腳
* task 的 input 是「呼叫這個 task 時要傳進來的參數」，性質比較接近程式語言裡函式的參數列表

### task / function 參數為什麼不用寫 reg
* module 的 port 跟 task/function 的參數，是兩套不同的規則：
  * module 的 port 是在描述「電路怎麼接」：input 預設是 wire（外部持續驅動），output 若要在 always/case 裡程序式賦值，必須額外宣告成 reg
  * task/function 的參數本質上是「呼叫時把值複製一份進來、用完就結束」，比較像程式語言裡函式的區域變數，不涉及電路接線的概念
* 因為 task/function 是 procedural（程序式）區塊，Verilog 語法規則直接讓它的 input/output/inout 參數天生具備變數（reg-like）性質，不需要另外寫 reg 宣告

### 呼叫 task 時是「傳值」，不是「型別繼承」
* task 的參數型別（變數）是宣告時就固定的，不會因為呼叫者傳進來的是 wire、reg 還是常數而改變
* 呼叫當下，Verilog 做的是把呼叫者傳進來的「值」複製一份到 task 內部的參數變數裡（類似傳值 pass-by-value），型別本身完全不受呼叫者影響
* 如果傳進去的值位寬跟參數宣告的不一樣，Verilog 會照一般賦值規則自動調整位寬（不夠補0、多的截斷），這跟型別是否為 reg 是兩件獨立的事，位寬調整的規則與 part select 提過的隱含截斷是同一套

### 實際應用：把重複的測試流程包成一個 check task
* 把「設定輸入 -> 等待 -> 比對結果 -> 印出 PASS/FAIL」這套固定流程包成一個 task，之後每測一組資料只要呼叫一次 `check(...)`，不用每次都手動複製貼上重複的程式碼

[回目錄](#toc)

---

<a id="m08d02"></a>

## 2026 年 8 月 2 日

## 今日成果探討：
### ALU 設計：
### 32bit_ALU_V1（Baseline） - 第一次 Synthesis + Implementation 結果，並記錄收斂到 WNS 接近 0 的結果
* Constraints sources - 1st
```
create_clock -period 20 -name clk [get_ports clk]
```

* Constraints sources - 2nd
```
create_clock -period 8 -name clk [get_ports clk]
```

* Constraints sources - 3rd
```
create_clock -period 7.2 -name clk [get_ports clk]
```

* Design sources（wrapper）
```verilog
module alu_v1_wrapper(
    input clk,
    input [31:0]a_in,
    input [31:0]b_in,
    input [2:0]op_in,
    output reg [31:0]res_out,
    output reg  [3:0]flag_out
);
    
    reg [31:0] a_r; // 鎖住輸入的暫存器，posedge clk 時取樣 a_in/b_in
    reg [31:0] b_r;
    reg [2:0] op_r; // 鎖住輸入的暫存器，posedge clk 時取樣 op_in
    wire [31:0] res_w; // alu_v1 算出的組合邏輯結果（尚未鎖存）
    wire [3:0] flag_w; // alu_v1 算出的組合邏輯 flag（尚未鎖存）
    
    always@(posedge clk)begin
        /*
        輸入暫存器：把當下的 a_in/b_in/op_in 取樣鎖住，讓 alu_v1 core 拿到的是穩定值
        ，而不是隨時可能變動的外部輸入
        */
        a_r <= a_in; 
        b_r <= b_in;
        op_r <= op_in;
        // 輸出暫存器：把 alu_v1 算好的組合邏輯結果取樣鎖住，才能輸出穩定的 res_out/flag_out
        res_out <= res_w;
        flag_out <= flag_w;
    end
    
    // alu_v1：純組合邏輯核心，接的是已經被鎖住的 a_r/b_r/op_r（不是原始輸入a_in、b_in、op_in）
    alu_v1 core(
    .a(a_r),
    .b(b_r),
    .op_code(op_r),
    .res(res_w),
    .flag(flag_w) 
    );

endmodule
```

* 模擬結果
1. period 20：
   * 反推 Fmax：`(20 - 10.789) = 9.211ns -> Fmax ≈ 1000 / 9.211 ≈ 108.6MHz`
<img width="1327" height="91" alt="image" src="https://github.com/user-attachments/assets/5a9e0bad-958f-477c-8a87-4f3f2e02cb07" />

2. period 8：
   * 反推 Fmax：`(8 − 0.970) = 7.030ns -> Fmax ≈ 1000 / 7.030 ≈ 142.2 MHz`
<img width="1326" height="88" alt="image" src="https://github.com/user-attachments/assets/abf51b0f-e8e4-490c-9b74-2e08b15c379e" />

3. period 7.2：
   * 反推 Fmax：`(7.2 − 0.516) = 6.684ns -> Fmax ≈ 1000 / 6.684 ≈ 149.6 MHz`
<img width="1327" height="99" alt="image" src="https://github.com/user-attachments/assets/a5696367-fd6b-4b48-9b5b-79145aaacbf5" />

### ALU_V1（Baseline）最終基準線數據
* Period = 7.2 ns, WNS = 0.516 ns
* Fmax ≈ 149.6 MHz
* LUT = 327, FF = 103

### ALU_V2（pipeline） - pipeline ALU 設計規劃
1. 在 alu_v1 中的 critical path
   * Barrel Shifter（桶型移位器）：最主要的 Critical Path
     * 邏輯結構： 採用 5 階串聯的條件選擇器（`b[0]` ~ `b[4]` 對應 1, 2, 4, 8, 16 bits 移位）。
	 * 延遲原因： 訊號必須**連續穿過 5 個 32-bit MUX**。每一個 MUX 的 Gate Delay 會**線性累加**，形成全模組最長的邏輯鏈（Logic Chain）。

   * 33-bit Add/Sub（加減法器）：次要瓶頸
	 * 邏輯結構： `assign add_sub = {1'b0, a} + {1 me0, b_op} + sub;`
	 * 延遲原因： 高位元（MSB）必須等待低位元（LSB）一路傳遞上來的**進位訊號（Carry Chain）**，需等待 32 個 Full Adder 的傳遞延遲。

   * 位元邏輯運算（AND / OR / XOR）：非瓶頸
	 * 邏輯結構： 32-bit 平行運算。
	 * 延遲原因： 僅需經過 **1 階邏輯門**，無位元間的依賴關係，延遲極短。

2. 流水線（Pipelining）切割策略
   * 為什麼不採用 16-bit / 16-bit 高低位拆分？
   	 * **加法器 Carry 依賴：** 高 16-bit 必須等待低 16-bit 的 Carry Out，橫向拆分無法打破時間依賴。
     * **移位器位元跨界：** 桶型移位器的資料會在 32-bit 空間內跨界移動，拆成高低 16-bit 會破壞移位邏輯。

   * 2-Stage Pipeline 切割方案 (縱向階段切割)
     * Stage 1：Shifter 前 3 階 (b[0]~b[2]: 1, 2, 4 bits 移位) ； 33-bit 加減法運算 (ADD / SUB / SLT 準備)
     * Stage 2：Shifter 後 2 階 (b[3]~b[4]: 8, 16 bits 移位)；SLT 邏輯與 Flag 生成 (Z, N, C, V)

   * 2-Stage Pipeline 切割方案原因
     * 時間延遲的「均等平分」（Timing Balance）
       * 桶型移位器總共有 5 階 MUX 選擇器
       * Stage 1 切前 3 階（1, 2, 4 bits）： 穿過 3 個 MUX。
       * Stage 2 切後 2 階（8, 16 bits）： 穿過 2 個 MUX ＋ 1 個最終輸出的 MUX（多路選擇 case）。
       * 讓 Stage 1 與 Stage 2 的邏輯門延遲（Gate Delay）平分
    
	 * 邏輯跨度與加法器同步
      * 加法器的延遲： 32-bit 的進位鏈（Carry Chain）傳播時間，大約剛好等於 2 ~ 3 個 MUX 的延遲。
      * 在 Stage 1：當移位器做完前 3 階（1, 2, 4 bits）時，33-bit 加法器也剛好算完！
      * 兩大運算單元可以在同一個時脈邊緣（Clock Edge） 一起將結果鎖進 Stage 1 暫存器。
    
	* 簡化 Stage 2 的 SLT 與 Flag 計算
	  * 因為加法器在 Stage 1 已經算完了 add_sub
      * Stage 2 就可以直接利用 Stage 1 留下來的 add / sub 結果來判斷溢位（Overflow）與產生 Flag，不會擠壓到 Stage 2 的時序。  

## 遇到的困難與解決方案：
### 問題：
### 為什麼純組合邏輯的 ALU 需要包一層 wrapper 才能做 STA
* `alu_v1` 沒有 clk，無法建立合法的「暫存器→暫存器」timing path
* Wrapper 額外做輸入暫存器（鎖住 a_in/b_in/op_in）與輸出暫存器（鎖住 res/flag），中間接純組合邏輯的 alu_v1，形成合法同步結構
* alu_v1 core 接的是已鎖存的 a_r/b_r/op_r，不是原始輸入 a_in/b_in/op_in，確保組合邏輯的輸入來源穩定，才能被 STA 正確分析
* clk 需要同時接到輸入暫存器與輸出暫存器兩邊

## 關鍵知識/詞彙：
### Synthesis/STA 路徑 vs 功能模擬路徑
* STA：RTL + XDC → Synthesis/Implementation → Timing Report（WNS/Fmax），input 只是被當成晶片接腳，不需要任何東西驅動，也不需要 testbench
* 模擬：RTL + Testbench（用 reg 主動驅動 input）→ Simulator → 波形/PASS-FAIL，目的是驗證邏輯對不對
* 兩條路徑完全獨立，互不需要

### WNS 與 Fmax 的關係
* Timing Report 不會直接列出 Fmax，只給 WNS，需要自己反推

### FPGA 底層架構：桶型移位器 vs. 加法器延遲觀念
1. 桶型移位器（Barrel Shifter）的延遲觀念
* 底層實現機制： 採用 FPGA 內部的 LUT（Look-Up Table, 查找表） 來實現多路選擇器（MUX）。
* 延遲特性：
	* 串聯加成 (Cascaded)： 32-bit 桶型移位器需要做 5 階判斷（1, 2, 4, 8, 16 bits），等於資料必須連續穿過 5 個 MUX。
 	* 佈線開銷 (Routing Delay)： 每次經過一個 MUX，資料都要走出 LUT、經過 FPGA 的通用內部連線，再進入下一個 MUX。

2. 加法器（32-bit Adder）的延遲觀念
* 底層實現機制： 使用 FPGA 晶片內部硬體預先刻好的「專用高速進位鏈」（Dedicated Carry Chain，如 CARRY4 / CARRY8 模組）。
* 延遲特性：
	* 免走通用佈線： 進位訊號（Carry bit）從 Bit 0 傳到 Bit 31 時，使用的是硬體矽晶圓上專屬的硬化線路，不佔用通用的 LUT 佈線資源。
	* 傳播速度極快： 專用進位鏈傳遞 1 bit 的延遲非常小（僅數十皮秒, picoseconds）。

3. 以常見的 FPGA 製程（如 Xilinx 7-Series）為例，兩者的邏輯延遲量級比較

| 比較項目 | 桶型移位器 (Barrel Shifter) | 32-bit 加法器 (Adder) |
| --- | --- | --- |
| **底層硬體資源** | 通用 LUT (Look-Up Table) + 通用連線 | **專用硬體進位鏈 (Dedicated Carry Chain)** |
| **訊號傳遞路徑** | 5 階 MUX 串聯鏈 (Cascade Chain) | 1 階 LUT 輸入 + 32-bit 硬體進位傳遞 |
| **實體佈線開銷** | 高 (每次跨 MUX 皆需走通用佈線) | 低 (走晶片預先刻好的專用高速通道) |
| **估算延遲時間** | 約 **2.0 ~ 2.5 ns** *(最慢)* | 約 **1.0 ~ 1.2 ns** |
| **相對延遲比例** | **100% (基準 Critical Path)** | **約為移位器的 50% (相當於 2~3 個 MUX)** |

[回目錄](#toc)

---

<a id="m08d03"></a>

## 2026 年 8 月 3 日

## 今日成果探討：
### ALU_V2（pipeline）設計：完成 RTL，共歷經 5 輪修正才達到邏輯正確
* Design sources
```verilog
module alu_v2(
    input clk,
    input rst_n,
    input [31:0]a,
    input [31:0]b,
    input [2:0]op_code,
    output reg[31:0]res,
    output reg[3:0]flag
);

// --- stag1：前半段運算　＋　暫存器（stag1 registers）---

    //構建3個 32bit 暫存器用於 barrel shifter
    //Shamt 選用 b[0] ~ b[2] 判斷移動幾個 bit
    wire [31:0]s1_l, s2_l, s3_l; 
    wire [31:0]s1_r, s2_r, s3_r;
    
    // 構建共用的 33-bit 加減法邏輯（在 case 外面算）
    wire sub;
    wire [31:0]b_op;
    wire [32:0]add_sub;

    assign sub = (op_code == 3'b001); // op_code == 3'b001，執行減法
    assign b_op = b ^ {32{sub}}; // 將 b 逐位元反相
    assign add_sub = {1'b0, a} + {1'b0, b_op} + sub; // ADD: a+b；SUB: a + ~b + 1（2補數，+1 由 sub 提供）
    
    // b[0] ~ b[2] 的 logic_shift_left
    assign s1_l = (b[0]) ? {a[30:0], 1'b0} : a;
    assign s2_l = (b[1]) ? {s1_l[29:0], 2'b00} : s1_l;
    assign s3_l = (b[2]) ? {s2_l[27:0], 4'b0000} : s2_l;
    
    // b[0] ~ b[2] 的 arithmetic_shift_right
    assign s1_r = (b[0]) ? {a[31], a[31:1]} : a;
    assign s2_r = (b[1]) ? {{2{a[31]}}, s1_r[31:2]} : s1_r;
    assign s3_r = (b[2]) ? {{4{a[31]}}, s2_r[31:4]} : s2_r;
    
    // 構建 stage1 -> stage2 中間暫存器
    reg [31:0]a_stg1, b_stg1;
    reg [2:0]op_code_stg1;
    reg [32:0]add_sub_stg1;
    reg [31:0]b_op_stg1;
    reg [31:0]shift_stg_l;
    reg [31:0]shift_stg_r;
    reg [31:0]res_stg1;
    
// 前半段資料 -> stage 1
always@(posedge clk)begin
    if(~rst_n)begin
        a_stg1 <= 0;
        b_stg1 <= 0;
        b_op_stg1 <= 0;
        op_code_stg1 <= 0;
        add_sub_stg1 <= 0;
        shift_stg_l <= 0;
        shift_stg_r <= 0;
        res_stg1 <= 0;
    end
    else begin
        a_stg1 <= a;
        b_stg1 <= b;
        b_op_stg1 <= b_op;
        op_code_stg1 <= op_code;
        
        // Stage 1 行為
        case(op_code)
            3'b000 : add_sub_stg1 <= add_sub; // +、- 法運算結果存儲
            3'b001 : add_sub_stg1 <= add_sub; // +、- 法運算結果存儲
            3'b010 : res_stg1 <= a & b;
            3'b011 : res_stg1 <= a | b;
            3'b100 : res_stg1 <= a ^ b;
            3'b101 : shift_stg_l <= s3_l; // b[0] ~ b[3] 移位結果存儲
            3'b110 : shift_stg_r <= s3_r; // b[0] ~ b[3] 移位結果存儲
            3'b111 : res_stg1 <= ($signed(a) < $signed(b)) ? 32'd1 : 32'd0;
            default : res_stg1 <= 0;
        endcase      
    end   
end
// --- stag1：前半段運算　＋　暫存器（stag1 registers）結束---

// --- stag2：後半段運算　＋　暫存器（stag2 registers）---
    
    //構建2個 32bit 暫存器用於 barrel shifter
    //Shamt 選用 b_stg1[3] ~ b_stg1[4] 判斷移動幾個 bit
    wire [31:0]s4_l, shift_l_res;
    wire [31:0]s4_r, shift_r_res;
    
    // b_stg1[3] ~ b_stg1[4] 的 logic_shift_left
    assign s4_l = (b_stg1[3]) ? {shift_stg_l[23:0], 8'b00000000} : shift_stg_l;
    assign shift_l_res = (b_stg1[4]) ? {s4_l[15:0], 16'b0000000000000000} : s4_l;
    // b_stg1[3] ~ b_stg1[4] 的 arithmetic_shift_right
    assign s4_r = (b_stg1[3]) ? {{8{a_stg1[31]}}, shift_stg_r[31:8]} : shift_stg_r;
    assign shift_r_res = (b_stg1[4]) ? {{16{a_stg1[31]}}, s4_r[31:16]} : s4_r;
    
    // 構建進位與溢位判斷
    wire carry_stg1;
    wire overflow_stg1;
    assign carry_stg1 = add_sub_stg1[32]; //進位判斷 
    assign overflow_stg1 = (~(a_stg1[31] ^ b_op_stg1[31])) && (a_stg1[31] ^ add_sub_stg1[31]); //溢位判斷
    
    // stage 1 -> stage 2（final_res）
    reg [31:0] final_res;
    always @(*) begin
        case(op_code_stg1)
            3'b000 : final_res = add_sub_stg1[31:0];
            3'b001 : final_res = add_sub_stg1[31:0];
            3'b010 : final_res = res_stg1;
            3'b011 : final_res = res_stg1;
            3'b100 : final_res = res_stg1;
            3'b101: final_res = shift_l_res;
            3'b110: final_res = shift_r_res;
            3'b111 : final_res = res_stg1;
            default: final_res = 0;
        endcase
    end
    
// stage 2 -> resault
always@(posedge clk)begin
    if(~rst_n)begin
        res <= 0;
        flag <= 0;
    end
    else begin
        res <= final_res; // 直接用統一選好的值，case 不用重複寫兩次
        // Z/N 對每種運算都更新，C/V 只在 ADD/SUB 才有意義
        flag <= {(final_res == 32'd0), final_res[31],
         ((op_code_stg1==3'b000)||(op_code_stg1==3'b001)) ? carry_stg1    : 1'b0,
         ((op_code_stg1==3'b000)||(op_code_stg1==3'b001)) ? overflow_stg1 : 1'b0};
    end   
end
// --- stag2：後半段運算　＋　暫存器（stag2 registers）結束---   
endmodule
```

## 遇到的困難與解決方案：
### 問題1：Stage1/Stage2 之間資料流沒有真的接起來
* 原因：Stage2 算移位結果時，用的是原始未鎖存的 `b`、`s3_l`、`s3_r`，跟 Stage1 鎖存的 `op_code_stg1`（已延遲一拍）時間點對不上，等於移位運算完全沒被 pipeline 到
* 解法：Stage1 額外做 `shift_stg_l`/`shift_stg_r` 兩個暫存器，把中間移位結果鎖存起來，Stage2 改用鎖存後的值繼續算

### 問題2：case/if 判斷式寫在 always block 的 if(reset)/else 外面，導致 reset 沒有真正生效
* 原因：同一個訊號在 reset 分支跟外層又被賦值一次，最後執行到的那次才生效，蓋掉 reset 設定的值
* 解法：把 case/if 都搬進對應的 else begin...end 裡面

### 問題3：flag 讀到「舊」的 Z/N/C/V 中繼暫存器（非阻塞賦值特性）
* 原因：`<=` 賦值右邊讀到的是這次賦值前的舊值，`flag <= {Z,N,C,V}` 會慢一拍
* 解法：改成直接用當下算出的訊號組合，不繞經中繼暫存器

### 問題4：Stage2 移位控制訊號（b[3] / b[4]）用的仍是原始未鎖存的 b
* 原因：`shift_stg_l`/`shift_stg_r` 是上一拍鎖存的資料，但判斷要不要繼續移的 `b[3]`/`b[4]` 卻是這一拍當下最新的 b，資料跟控制時間點不一致
* 解法：改成 `b_stg1[3]`/`b_stg1[4]`

### 問題5：sub_stg1 沒有被鎖存，b_op_stg1 用當下的 sub 去配鎖存過的 b_stg1
* 原因：控制訊號跟資料時間點不一致，跟問題5是同一類問題
* 解法：Stage1 額外鎖存 `sub_stg1`，Stage2 改用 `b_op_stg1 = b_stg1 ^ {32{sub_stg1}}`

### 問題6：ADD / SUB 的 flag 讀錯來源（反覆出現在第1、2版，第3版才真正解決）
* 原因：ADD/SUB 的真正結果存在 `add_sub_stg1`，但 flag 判斷 Z/N 時卻讀取 `res_stg1`（該次 case 沒有對應分支，值是殘留的舊值），導致 ADD/SUB 的 Z/N flag 完全錯誤
* 解法：在 Stage2 用組合邏輯統一算出 `final_res`（依 op_code_stg1 選出這一拍真正的最終結果，ADD/SUB 選 add_sub_stg1，其餘選對應暫存器），res 跟 flag 的 Z/N 都從 final_res 取值，不再各自各的

### 問題7：flag 只有 ADD / SUB 才更新，其他運算 flag 維持舊值不動
* 原因：整個 flag（含 Z/N）都包在 `if(ADD||SUB)` 裡面才更新，跟 baseline 行為（Z/N 每種運算都要算，只有 C/V 限定 ADD/SUB）不一致
* 解法：拿掉 Z/N 的 if 限制，改成永遠算 Z/N，只有 C/V 用三元運算子限定 ADD/SUB 才給實際值、否則補 0

## 關鍵知識/詞彙：
### Pipeline 設計中「控制訊號要跟資料同一拍」的原則
* 切 pipeline 時，Stage2 用來判斷「怎麼處理某個資料」的控制訊號（例如 op_code、b 的某幾個 bit、sub 這種旗標），一定要用「跟該筆資料同一拍被鎖存」的版本，不能混用「這一拍最新的」控制訊號去配「上一拍鎖存」的資料
* 這是這次 debug 過程中最常出現、也最關鍵的一種錯誤模式，移位邏輯（b[3]/b[4]）跟 overflow 判斷（sub）都各踩到一次

### 非阻塞賦值（<=）讀值的時機
* `<=` 賦值時，右邊讀到的是這次賦值前的舊值，賦值本身要等整個 block 結束才真的生效
* 如果讓 flag 這種輸出訊號繞經中繼暫存器（如 Z/N/C/V）再組合，容易讀到「慢一拍」的舊值，建議直接用當下算出的訊號組合，避免多繞一層

[回目錄](#toc)

---

<a id="m08d04"></a>

## 2026 年 8 月 4 日

## 今日成果探討：
### ALU_V2（pipeline）設計：
1. 完成 Testbench - alu_v2_tt，驗證 pipeline 版本功能，經多輪除錯後全數 PASS
2. 確認延遲精確為 2 個 clock cycle，跟設計預期的 2-stage pipeline 一致，功能上與 baseline（alu_v1）等價

* Simulation sources
  ```verilog
  module alu_v2_tt();
    
    reg clk;
    reg rst_n;
    reg [31:0]a, b;
    reg [2:0]op_code;
    wire [31:0]res;
    wire [3:0]flag;
    integer i;
    
    integer error_count; // 用於計算"error"次數
    integer test_count; // 用於計算"test"次數
    
    alu_v2 tt(
    .clk(clk),
    .rst_n(rst_n),
    .a(a),
    .b(b),
    .op_code(op_code),
    .res(res),
    .flag(flag)
    );
    
    task automatic check(
        input [31:0]t_a, // a的測試變數
        input [31:0]t_b, // b的測試變數
        input [2:0]t_op, // op_code的測試變數
        input [31:0]t_res, // res的測試變數
        input t_z, t_n, t_c, t_v // flag的測試變數
    );
        begin
            a = t_a; b = t_b; op_code = t_op;
            
            @(posedge clk); // 第1拍：鎖進 Stage1
            @(posedge clk); // 第2拍：鎖進 Stage2
            #1; // 確保讀值時 NBA（非阻塞賦值） 已經真正落地
            
            test_count = test_count + 1; // 每次開始測試 test_count 次數 +1
            if(res !== t_res || flag !== {t_z, t_n, t_c, t_v})begin // res 結果或 flag 結果不符
                error_count = error_count + 1; // 錯誤就把 error_count 次數 +1
                $display("[FAIL]：%0d, op_code = %b, a = %h, b = %h | got res = %h, flag = %b | t_res = %h, flag = %b",
                test_count, t_op, t_a, t_b, res, flag, t_res, {t_z, t_n, t_c, t_v});
            end
            else begin
                $display("[PASS]：%0d, op_code = %b", test_count, t_op);
            end
        end
    endtask
    
    initial clk = 0;
    always #5 clk = ~clk;   // 每5ns翻轉一次，週期10ns

    initial begin
        rst_n = 0;
        repeat (2) @(posedge clk);   // 讓 reset 訊號至少過2個clock edge
        rst_n = 1;
        @(posedge clk); // 多留一拍緩衝，讓 reset 解除跟第一筆資料錯開
        
        error_count = 0;
        test_count = 0;
        
        // 基本功能：ADD / SUB 
        check(32'h0000_00F0, 32'h0000_000F, 3'b000, 32'h0000_00FF, 0,0,0,0); // ADD
        check(32'h0000_00F0, 32'h0000_000F, 3'b001, 32'h0000_00E1, 0,0,1,0); // SUB
        
        // 邏輯運算：AND / OR / XOR
        check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b010, 32'h0F00_0F00, 0,0,0,0); // AND
        check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b011, 32'hFF0F_FF0F, 0,1,0,0); // OR
        check(32'hFF00_FF00, 32'h0F0F_0F0F, 3'b100, 32'hF00F_F00F, 0,1,0,0); // XOR
        
        // 針對電路容易出錯的地方各自設計測資
        check(32'hF123_4567, 32'h0000_0004, 3'b110, 32'hFF12_3456, 0,1,0,0); // SRA 符號延伸
        check(32'h7FFF_FFFF, 32'h0000_0001, 3'b000, 32'h8000_0000, 0,1,0,1); // ADD overflow
        check(32'hFFFF_FFFF, 32'h0000_0005, 3'b111, 32'h0000_0001, 0,0,0,0); // SLT：-1 < 5
        check(32'h0000_0001, 32'h0000_0000, 3'b101, 32'h0000_0001, 0,0,0,0); // shamt=0
        check(32'h0000_0001, 32'h0000_001F, 3'b101, 32'h8000_0000, 0,1,0,0); // shamt=31
        
        $display("\n測試完成：共 %0d 組，失敗 %0d 組", test_count, error_count);
        if(error_count == 0)begin
            $display("ALL TESTS PASSED");
        end
        $finish;       
    end  
  ```

* 模擬結果
<img width="213" height="257" alt="image" src="https://github.com/user-attachments/assets/e6e79d99-8720-451a-ae4d-08f9bbb5e3b7" />

## 遇到的困難與解決方案：
### 問題1：clk 沒有持續產生方波
* 原因：只寫了 `#10 clk = ~clk;` 一次，之後沒有任何機制讓 clk 持續振盪，DUT 完全不會觸發
* 解法：改成 `initial clk = 0; always #5 clk = ~clk;` 持續產生週期性方波

### 問題2：check() 沒有等待 clock edge，也沒處理 pipeline 延遲
* 原因：賦值後立刻比對，沒有等任何 posedge clk；alu_v2 是 2-stage pipeline，結果要 2 拍後才會反映到 res/flag
* 解法：在賦值與比對之間加上 `@(posedge clk)` 兩次

### 問題3：reset 沒有撐過完整 clock cycle 就結束
* 原因：`rst_n = 0` 之後沒有等待任何 clock edge 就直接動作
* 解法：用 `repeat(2) @(posedge clk);` 讓 reset 訊號至少過 2 個 clock edge 再解除

### 問題5（花最多輪才找到原因）：加了正確拍數還是持續 FAIL，且結果連續多輪完全相同
* 排查過程：一開始懷疑是 Vivado 沒有重新編譯到最新程式碼（用 Relaunch、Reset Output Products、甚至手動清除 xsim 編譯產物都懷疑過），但檢查 RTL 、改了多次 testbench，數值依然完全相同
* 真正原因：**NBA（非阻塞賦值）取樣時機的競爭條件（race condition）**—— 在 RTL 的 DUT 內 `res <= final_res;` 是非阻塞賦值，實際生效發生在該次 posedge 的 NBA 更新區；但 testbench 裡 `@(posedge clk);` 恢復執行後緊接著的 blocking 讀值敘述，發生時機比 NBA 真正生效還早，讀到的是「這次更新前」的舊值
* 解法：在最後一次 `@(posedge clk);` 之後加上 `#1;`，確保讀值時 NBA 已經真正落地，加上後全數 PASS

## 關鍵知識/詞彙：
### NBA（非阻塞賦值）與讀值時機的競爭條件
* `<=` 賦值在硬體上代表暫存器行為，模擬時的生效時機是在該次事件的 NBA 更新區，晚於同一時間點的 blocking 敘述
* 如果 testbench 在 `@(posedge clk)` 恢復執行後，立刻用 blocking 敘述去讀 DUT 的輸出訊號，有可能讀到「這次 edge 更新前」的舊值，因為讀值時機比 NBA 真正生效還早
* 標準解法：在讀值前多留一個極小延遲（如 `#1`），或改成在 `@(negedge clk)` 讀值，確保 NBA 已經真正落地

### Debug 過程的啟示
* 「結果不對」不一定代表「設計邏輯錯了」，也可能是「觀察／取樣的時機不對」，兩者要分開排查，不要一路只往 RTL 邏輯或環境快取的方向找

[回目錄](#toc)

---

<a id="m08d05"></a>

## 2026 年 8 月 5 日

## 今日成果探討：
### ALU 設計：
### 32bit_ALU_V2（pipeline） - 第一次 Synthesis + Implementation 結果，並記錄收斂到 WNS 接近 0 的結果
* Constraints sources - 1st
```
create_clock -period 20 -name clk [get_ports clk]
```

* Constraints sources - 2nd
```
create_clock -period 6 -name clk [get_ports clk]
```

* Constraints sources - 3rd
```
create_clock -period 5 -name clk [get_ports clk]
```

* Constraints sources - 4th
```
create_clock -period 4.5 -name clk [get_ports clk]
```

* 模擬結果
1. period 20：
   * 反推 Fmax：`(20 - 14.489) = 5.511ns -> Fmax ≈ 1000 / 5.511 ≈ 181.5 MHz`
<img width="1336" height="89" alt="image" src="https://github.com/user-attachments/assets/9c8a5dec-93aa-4fea-8864-44167cd95726" />

2. period 6：
   * 反推 Fmax：`(6 − 1.327) = 4.673ns -> Fmax ≈ 1000 / 4.673 ≈ 214.0 MHz`
<img width="1335" height="89" alt="image" src="https://github.com/user-attachments/assets/5c21149f-dcb8-46ba-be32-c51a4055ca4b" />

3. period 5：
   * 反推 Fmax：`(5 − 0.751) = 4.249ns -> Fmax ≈ 1000 / 4.249 ≈ 235.3 MHz`
<img width="1334" height="88" alt="image" src="https://github.com/user-attachments/assets/a1f26036-345d-4680-9d55-7353304710fe" />

4. period 4.5：
   * 反推 Fmax：`(4.5 − 0.551) = 3.949ns -> Fmax ≈ 1000 / 3.949 ≈ 253.2 MHz`
<img width="1334" height="90" alt="image" src="https://github.com/user-attachments/assets/2dbb2fc8-a190-44de-8fe9-cfb091fc207f" />

### ALU_V2（Pipeline）最終基準線數據
* Period = 4.5 ns, WNS = 0.551 ns
* Fmax ≈ 253.2 MHz
* LUT = 317, FF = 172

### Baseline vs Pipeline 最終對照表

| 項目 | Baseline（alu_v1） | Pipeline（alu_v2, 2-stage） |
|---|---|---|
| Period（收斂值） | 7.2 ns | 4.5 ns |
| WNS | 0.516 ns | 0.551 ns |
| Fmax | ≈ 149.6 MHz | ≈ 253.2 MHz |
| LUT | 327 | 317 |
| FF | 103 | 172 |
| Latency | 1 cycle | 2 cycle |

### 說明
1. Baseline 版本是純組合邏輯，加法器用 Ripple Carry Adder，關鍵路徑是進位一路傳到最高位，限制了整體時脈上限，收斂後 Fmax 約 149.6MHz。
   
2. Pipeline 版本把資料路徑切成 2 個 stage，中間插入暫存器，把原本一次算完的長路徑拆成兩段較短的路徑，收斂後 Fmax 約 253.2MHz，相較 baseline 提升約 1.69 倍。
   
3. trade-off ：
   * 用「面積換取速度」換來的：FF 用量從 103 增加到 172（多了約 69 顆暫存器，用於 Stage1/Stage2 之間鎖存中繼資料）
   * LUT 用量反而略降（327→317），代表邏輯本身沒有變複雜，資源增加主要來自新增的暫存器，不是額外的運算邏輯。
   * Latency 則從 1 cycle 增加為 2 cycle
   * 犧牲單筆資料的延遲，換取整體吞吐量（throughput）與可運作頻率的提升。

4. 兩個版本的功能都用同一組 testbench 驗證過，結果完全等價，確保這組時序數據的比較是建立在功能正確、公平的基準。

## 未來優化方向（依 Timing Report 的 Net/Logic Delay 比例修正）
1. Timing Report
<img width="1062" height="387" alt="image" src="https://github.com/user-attachments/assets/6beb2c5e-173a-4285-907e-b0ef765f42f5" />

* Worst Path 前幾名的 Net Delay 都明顯大於 Logic Delay（例如 Path1: Logic=1.202ns, Net=2.611ns），且 High Fanout 高達16~32
* 代表瓶頸主要來自控制訊號（op_code_stg1、b_stg1[4]）扇出過大造成的繞線延遲

2. 優化方向：降低高扇出控制訊號的負載，而非搬動運算邏輯
* op_code_stg1 要同時驅動 Stage2 裡的移位選擇、final_res 的 8-way mux、flag 邏輯，扇出大
* 可以考慮的做法：
  * 在 Stage1 鎖存後，提早把 op_code_stg1 解碼成 one-hot 控制訊號，讓每條下游邏輯只接自己需要的那一條 select 線，而不是所有邏輯都共用同一組 3-bit bus，降低單一訊號的扇出數
  * 對高扇出訊號下 MAX_FANOUT 屬性限制，讓 Vivado 在 synthesis 階段主動做訊號複製（從 `res_reg[31]_lopt_replica` 這個命名可以看出，Vivado 已自動幫 res_reg[31] 做過一次複製優化，代表工具本身也判斷這是扇出問題，可以再手動加強）
* 優化不能只停留在架構設計，實際的 layout/繞線行為也會回頭影響該怎麼調整 RTL

## 關鍵知識/詞彙：
### 降低扇出的兩種做法
1. **提早解碼成 one-hot**：把一個多用途、被到處讀取的訊號（例如 op_code），提前轉換成多條各自獨立、各自代表單一意義的線（is_add、is_sub...），讓下游邏輯各自只接自己需要的那一條，分散單一訊號的驅動負擔，屬於 RTL 設計面的優化
2. **MAX_FANOUT 屬性**：透過 XDC 對特定訊號下扇出限制的約束，讓 Vivado 自動把超過門檻的訊號複製成多份分擔負擔，屬於工具層面的輔助手段，不需要更動 RTL 邏輯

### 為何產生_replica 
<img width="328" height="58" alt="image" src="https://github.com/user-attachments/assets/ce8cfae4-3dcd-429b-a2a8-a684708bfdd5" />

* Vivado 有時會自動判斷扇出過大並主動做訊號複製，產生類似 `_replica` 命名的訊號，代表工具本身已經在處理這類問題，可以透過約束加強這個機制

### Timing Report 的 Logic Delay vs Net Delay
* Logic Delay：訊號經過邏輯閘本身運算所花的時間
* Net Delay：訊號在實體接線上傳遞所花的時間（受扇出、繞線距離影響）
* 兩者比例可以幫助判斷關鍵路徑慢的根本原因：Logic Delay 高代表邏輯層數太深，Net Delay 高代表扇出/繞線是瓶頸，優化方向會完全不同

[回目錄](#toc)

---
<a id="m08d10"></a>

## 2026 年 8 月 10 日

## 今日進度：
### 資料：
1. Digital Design and Computer Architecture (ARM / RISC-V Edition) - ALU 管道化（Pipelining）、組合邏輯收斂與資源共享（Resource Sharing）、One-Hot State/Decoder 編碼優化。
2. Advanced ASIC Chip Synthesis: Using Synopsys® Design Compiler® Physical Compiler® and PrimeTime® - High Fanout 訊號對物理佈線（Routing）的寄生電容效應、Net Delay vs. Logic Delay 的消長關係、Timing Closure 調校策略。
3. Verilog HDL: A Guide to Digital Design and Synthesis - always @(*) 組合邏輯與推導 Flip-Flop（Clock Enable / Latch 避免）的 RTL 最佳撰寫範本。
4. AMD / Xilinx Vivado Design Suite User Guide: Design Analysis and Closure Techniques (UG906) - Section: High Fanout Net Optimization / Timing Report Analysis
5. AMD / Xilinx Vivado Design Suite User Guide: Synthesis (UG901) - Synthesis Attributes: max_fanout & Resource Sharing
6. Intel (Altera) Quartus Prime Standard Edition Handbook Volume 2: Design Implementation and Optimization - Section: Duplicate Registers for Fanout Control


## 關鍵知識/詞彙：
### FPGA/ASIC 硬體設計與 Timing Closure 優化指南

### 扇出（Fanout）與其影響
* **扇出 (Fanout)**：指單一輸出訊號（如暫存器 Q 端或邏輯閘輸出）所驅動的**下游輸入端（Loads）總數量**。
* **物理影響**：
  * **電容效應 (Capacitance)**：驅動的負載越多，金屬線路的總寄生電容越大，訊號充放電變慢（Slew Rate 惡化）。
  * **長途佈線 (Routing Distance)**：訊號需要透過 FPGA 的 Interconnect 金屬線拉去晶片上多個分散的角落。
* **現象**：Timing Report 中呈現 **$\text{Net Delay} \gg \text{Logic Delay}$**（佈線延遲遠大於邏輯延遲）。

### 降低扇出（High Fanout）的核心做法

* 解決高扇出問題通常從 **「RTL 架構設計面」** 與 **「EDA 工具約束面」** 兩大維度切入：

1. 方案一：RTL 設計面 — 提早解碼 / 集中收斂 (Architecture Optimization)

* **概念**：將原本多用途、多位元的編碼訊號（如 3-bit `op_code`），由「末端被動詢問」改為「源頭一次解完/集中收斂」。
* **優化方式**：
  1. **One-Hot 解碼**：在源頭將訊號轉換為獨立專線（`is_add`, `is_sub`），讓各子模組只接收專屬控制線，分散負載。
  2. **組合邏輯收斂 (Multiplexing)**：在進入下一級 DFF 前，透過 `always @(*)` 的 MUX 先將各模組算好的結果收斂成單一匯流排（如 `final_res`）。
* **優點**：
  * **大幅降低扇出**：控制訊號負載從 $N$ 降至個位數。
  * **資源共享 (Resource Sharing)**：避免 EDA 工具合成出多套重複的大型 MUX，顯著**節省 LUT 資源**。
  * **消除 Enable 腳位**：避免合成出帶有 Clock Enable (CE) 的多餘 Flip-Flop 控制線。

2. 方案二：工具約束面 — `MAX_FANOUT` 屬性 (Implementation Optimization)

* **概念**：不更動 RTL 邏輯，透過 XDC 約束或 RTL 屬性指示合成工具（如 Vivado）自動進行 **暫存器複製（Register Duplication）**。
* **運作機制**：
  ```text
  [原本] Reg_A ─────────────────► 負載 1 ~ 100 (Fanout = 100, Net Delay 爆表)

  [複製後] Reg_A_1 ─────────────► 負載 1 ~ 50  (Fanout = 50)
           Reg_A_2 (Replica) ───► 負載 51 ~ 100 (Fanout = 50)
  ```
* 範例程式：
	* Verilog RTL 屬性
 	```
  	(* max_fanout = 16 *) reg [2:0] op_code_stg1;
  	``` 

   * XDC 約束檔
	```
     set_property MAX_FANOUT 16 [get_nets op_code_stg1_reg[*]]
    ```
  

[回目錄](#toc)

---
<a id="m08d11"></a>

## 2026 年 8 月 11 日

## 今日成果探討：
### ALU 設計優化：
### 32bit_ALU_V2（pipeline） - 主動重構，把邏輯運算與 SLT 先獨立拆成一個組合邏輯，Stage1 暫存器改成**每拍無條件更新**：

* 程式碼
```verilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 2026/08/03 10:00:47
// Design Name: 
// Module Name: alu_v2
// Project Name: 
// Target Devices: 
// Tool Versions: 
// Description: 
// 
// Dependencies: 
// 
// Revision:
// Revision 0.01 - File Created
// Additional Comments:
// 
//////////////////////////////////////////////////////////////////////////////////


module alu_v2(
    input clk,
    input rst_n,
    input [31:0]a,
    input [31:0]b,
    input [2:0]op_code,
    output reg[31:0]res,
    output reg[3:0]flag
);

// --- stag1：前半段運算　＋　暫存器（stag1 registers）---

    //構建3個 32bit 暫存器用於 barrel shifter
    //Shamt 選用 b[0] ~ b[2] 判斷移動幾個 bit
    wire [31:0]s1_l, s2_l, s3_l; 
    wire [31:0]s1_r, s2_r, s3_r;
    
    // 構建共用的 33-bit 加減法邏輯（在 case 外面算）
    wire sub;
    wire [31:0]b_op;
    wire [32:0]add_sub;

    assign sub = (op_code == 3'b001); // op_code == 3'b001，執行減法
    assign b_op = b ^ {32{sub}}; // 將 b 逐位元反相
    assign add_sub = {1'b0, a} + {1'b0, b_op} + sub; // ADD: a+b；SUB: a + ~b + 1（2補數，+1 由 sub 提供）
    
    // b[0] ~ b[2] 的 logic_shift_left
    assign s1_l = (b[0]) ? {a[30:0], 1'b0} : a;
    assign s2_l = (b[1]) ? {s1_l[29:0], 2'b00} : s1_l;
    assign s3_l = (b[2]) ? {s2_l[27:0], 4'b0000} : s2_l;
    
    // b[0] ~ b[2] 的 arithmetic_shift_right
    assign s1_r = (b[0]) ? {a[31], a[31:1]} : a;
    assign s2_r = (b[1]) ? {{2{a[31]}}, s1_r[31:2]} : s1_r;
    assign s3_r = (b[2]) ? {{4{a[31]}}, s2_r[31:4]} : s2_r;
    
    // 構建 stage1 -> stage2 中間暫存器
    reg [31:0]a_stg1, b_stg1;
    reg [2:0]op_code_stg1;
    reg [32:0]add_sub_stg1;
    reg [31:0]b_op_stg1;
    reg [31:0]shift_stg_l;
    reg [31:0]shift_stg_r;
    reg [31:0]res_stg1;
    
    
    reg [31:0]res_com1;
    always@(*)begin
        case(op_code)
            3'b010 : res_com1 = a & b;
            3'b011 : res_com1 = a | b;
            3'b100 : res_com1 = a ^ b;
            3'b111 : res_com1 = ($signed(a) < $signed(b)) ? 32'd1 : 32'd0;
            default : res_com1 = 0;
        endcase
    end
    
    
    // 前半段資料 -> stage 1
    always@(posedge clk)begin
        if(~rst_n)begin
            a_stg1 <= 0;
            b_stg1 <= 0;
            b_op_stg1 <= 0;
            op_code_stg1 <= 0;
            add_sub_stg1 <= 0;
            shift_stg_l <= 0;
            shift_stg_r <= 0;
            res_stg1 <= 0;
        end
        else begin
            a_stg1 <= a;
            b_stg1 <= b;
            b_op_stg1 <= b_op;
            op_code_stg1 <= op_code;
 
            add_sub_stg1 <= add_sub; // +、- 法運算結果存儲
            shift_stg_l <= s3_l; // b[0] ~ b[3] 移位結果存儲
            shift_stg_r <= s3_r; // b[0] ~ b[3] 移位結果存儲
            
            res_stg1 <= res_com1;     
        end   
    end
    // --- stag1：前半段運算　＋　暫存器（stag1 registers）結束---

    // --- stag2：後半段運算　＋　暫存器（stag2 registers）---
    
    //構建2個 32bit 暫存器用於 barrel shifter
    //Shamt 選用 b_stg1[3] ~ b_stg1[4] 判斷移動幾個 bit
    wire [31:0]s4_l, shift_l_res;
    wire [31:0]s4_r, shift_r_res;
    
    // b_stg1[3] ~ b_stg1[4] 的 logic_shift_left
    assign s4_l = (b_stg1[3]) ? {shift_stg_l[23:0], 8'b00000000} : shift_stg_l;
    assign shift_l_res = (b_stg1[4]) ? {s4_l[15:0], 16'b0000000000000000} : s4_l;
    // b_stg1[3] ~ b_stg1[4] 的 arithmetic_shift_right
    assign s4_r = (b_stg1[3]) ? {{8{a_stg1[31]}}, shift_stg_r[31:8]} : shift_stg_r;
    assign shift_r_res = (b_stg1[4]) ? {{16{a_stg1[31]}}, s4_r[31:16]} : s4_r;
    
    // 構建進位與溢位判斷
    wire carry_stg1;
    wire overflow_stg1;
    assign carry_stg1 = add_sub_stg1[32]; //進位判斷 
    assign overflow_stg1 = (~(a_stg1[31] ^ b_op_stg1[31])) && (a_stg1[31] ^ add_sub_stg1[31]); //溢位判斷
    
    // stage 1 -> stage 2（final_res）
    reg [31:0] final_res;
    always @(*) begin
        case(op_code_stg1)
            3'b000 : final_res = add_sub_stg1[31:0];
            3'b001 : final_res = add_sub_stg1[31:0];
            3'b010 : final_res = res_stg1;
            3'b011 : final_res = res_stg1;
            3'b100 : final_res = res_stg1;
            3'b101: final_res = shift_l_res;
            3'b110: final_res = shift_r_res;
            3'b111 : final_res = res_stg1;
            default: final_res = 0;
        endcase
    end
    
    // stage 2 -> resault
    always@(posedge clk)begin
        if(~rst_n)begin
            res <= 0;
            flag <= 0;
        end
        else begin
            res <= final_res; // 直接用統一選好的值，case 不用重複寫兩次
            // Z/N 對每種運算都更新，C/V 只在 ADD/SUB 才有意義
            flag <= {(final_res == 32'd0), final_res[31],
             ((op_code_stg1==3'b000)||(op_code_stg1==3'b001)) ? carry_stg1    : 1'b0,
             ((op_code_stg1==3'b000)||(op_code_stg1==3'b001)) ? overflow_stg1 : 1'b0};
        end   
    end
    // --- stag2：後半段運算　＋　暫存器（stag2 registers）結束---   
    
endmodule	
```

* 模擬結果
<img width="287" height="309" alt="image" src="https://github.com/user-attachments/assets/640080aa-90f9-4679-a364-16d5d0bfe988" />
<img width="1901" height="105" alt="image" src="https://github.com/user-attachments/assets/57a03d44-5b31-4280-8c29-92d10fc70e0e" />
<img width="1645" height="390" alt="image" src="https://github.com/user-attachments/assets/35c5b00a-2fd0-4700-b99a-9d226f4368c4" />

### ALU_V2（Pipeline）基準線數據
* Period = 4.5 ns, WNS = 0.473 ns
* Fmax ≈ (4.5-0.473)=4.027ns → Fmax≈248.3MHz MHz
* LUT = 392, FF = 171

* 效果：四個暫存器每拍都拿到組合邏輯即時算出的新鮮值，徹底排除「case 分支沒列到、讀到殘留舊值」這類問題，也避免被合成器推斷成帶 load-enable 的非預期結構；Stage2 仍靠 `op_code_stg1` 從 `final_res` 正確挑選來源，功能不受影響
* Trade-off：每拍所有暫存器都會翻動（即使跟當拍運算無關），會多耗一點動態功耗，是刻意的簡潔度/穩定度換功耗的設計取捨


## 關鍵知識/詞彙
### 下階段優化考量
1. PPA / 暫存器面積優化：精簡 Stage 1 至 Stage 2 跨級暫存器

* **原設計瓶頸 (Baseline Bottleneck)：**
  觀察 Stage 2 邏輯發現，跨級暫存器存在大量的無效鎖存（Redundant Latching）：
  * `a_stg1` (32-bit)：在 Stage 2 僅用於最高位元 `a_stg1[31]`（算術右移 Sign bit 與溢位 $V$ 判斷）。
  * `b_stg1` (32-bit)：在 Stage 2 僅用於 `b_stg1[4:3]`（Barrel Shifter 後兩階移位）。

* **優化策略 (Optimization Strategy)：**
  取消完整 32-bit 暫存器的宣告，改為僅精準鎖存所需的關鍵位元：
  * `a_sign_stg1` (1-bit) $\leftarrow$ `a[31]`
  * `shamt_stg2` (2-bit) $\leftarrow$ `b[4:3]`

* **改善效益 (Benefits)：**
  在 RTL 層級直接**削減 61 個 Flip-Flops (FF)**。雖然 EDA 工具（如 Vivado Synthesis）具備 Register Pruning 機制，但由設計者在 RTL 主動精確宣告，能確保硬體架構的確定性並降低邏輯合成風險。

2. 邏輯運算單元優化：SLT (Set Less Than) 共享加減法器結果

* **原設計瓶頸 (Baseline Bottleneck)：**
  在 Stage 1 中使用 `$signed(a) < $signed(b)` 進行有號數比較，會導致綜合工具（Synthesis Tool）額外合成出一組獨立的 32-bit 符號比較器（Signed Comparator），增加不必要的晶片面積與 Critical Path 延遲。

* **優化策略 (Optimization Strategy)：**
  利用補數算術邏輯，有號數比較 $A < B$ 在數學上完全等同於判斷 $A - B$ 結果的**負號（Sign Bit, $S_{31}$）與溢位（Overflow, $V$）**。SLT 運算可直接移至 Stage 2，共享加減法器的運算結果：

  $$\text{SLT} = S_{31} \oplus V$$

* **改善效益 (Benefits)：**
  直接**省去 Stage 1 一組 32-bit 比較器**的硬體邏輯閘與連線延遲，有效減輕 Stage 1 的組合邏輯負載並提升時脈頻率（$F_{max}$）。


### SLT (Set Less Than) 邏輯運算與硬體實現原理

* 在 2 補數算術邏輯單元（ALU）中，有號數大小比較 $A < B$（即 SLT 指令）可直接共享加減法器的運算結果，透過 **減法結果符號位元（Sign Bit, $S_{31}$）** 與 **溢位旗標（Overflow Flag, $V$）** 的 XOR 運算精準導出：

	* $$ \text{SLT} = S_{31} \oplus V $$
	* 其中：
		* $S_{31}$：減法運算結果的最高位元（`add_sub_stg1[31]`）
		* $V$：減法運算溢位旗標（`overflow_stg1`）
---

1. 判斷原理 (Core Principle)

判斷 $A < B$ 在計算機中等同於評估減法 $D = A - B$ 的結果是否為負數（$D < 0$）。

符號位元 $S_{31}$ 是否能正確反映結果的正負，取決於運算過程是否產生**溢位（Overflow）**：

### A. 無溢位發生 ($V = 0$)
減法結果落在 32-bit 有號數表示範圍內，$S_{31}$ 能真實代表運算結果的正負號：
* **$S_{31} = 1$**：代表 $A - B < 0 \implies A < B$，即 $\text{SLT} = 1$。
* **$S_{31} = 0$**：代表 $A - B \ge 0 \implies A \ge B$，即 $\text{SLT} = 0$。

> **邏輯公式：** $\text{SLT} = S_{31} \oplus 0 = S_{31}$

### B. 發生溢位 ($V = 1$)
溢位僅會發生於「異號相減」（正數減負數或負數減正數）。當 $V = 1$ 時，說明運算結果已超出 32-bit 表示極限，導致 $S_{31}$ 發生位元反轉（錯誤的符號），必須透過 XOR $V$ 修正：

* **狀況一：正數減負數 ($A \ge 0, B < 0$)**
  * **數學本質：** $A$ 必定大於 $B$，因此 $A < B$ 應為 **假 ($0$)**。
  * **硬體現象：** $A - B$ 正數數值過大產生**上溢（Overflow）**，導致 $S_{31} = 1$（錯誤地呈現負號）。
  * **XOR 校正：** $S_{31} \oplus V = 1 \oplus 1 = \mathbf{0}$ （成功修正為正確的 $0$）。

* **狀況二：負數減正數 ($A < 0, B \ge 0$)**
  * **數學本質：** $A$ 必定小於 $B$，因此 $A < B$ 應為 **真 ($1$)**。
  * **硬體現象：** $A - B$ 負數數值過小產生**下溢（Underflow）**，導致 $S_{31} = 0$（錯誤地呈現正號）。
  * **XOR 校正：** $S_{31} \oplus V = 0 \oplus 1 = \mathbf{1}$ （成功修正為正確的 $1$）。

2. 真值表總結 (Truth Table)

| 溢位旗標 ($V$) | 符號位元 ($S_{31}$) | 硬體實際產生的現象 | 數學真實狀況 | $\text{SLT} = S_{31} \oplus V$ |
| :---: | :---: | :--- | :--- | :---: |
| **0** | **0** | 無溢位，結果為正數或 0 | $A \ge B$ | **0** |
| **0** | **1** | 無溢位，結果為負數 | $A < B$ | **1** |
| **1** | **0** | 負減正發生下溢，結果被拉回正數 | $A < B$ | **1** |
| **1** | **1** | 正減負發生上溢，結果被拉回負數 | $A \ge B$ | **0** |
---

## 3. 硬體效益 (Hardware Benefit)

* 利用 $S_{31} \oplus V$：
1. **面積優化：** 僅需額外增加一個 **2-input XOR 邏輯閘**，無需合成複雜的 32-bit 專用符號比較器（Signed Comparator）。
2. **時序優化：** 完全共享加減法器（Adder/Subtractor）的運算路徑，大幅節省晶片面積並優化關鍵路徑（Critical Path）延遲。

[回目錄](#toc)

---
<a id="m08d12"></a>

## 2026 年 8 月 12 日

## 今日進度：
### 刷題：複習 HDLBits 7/3 - 8/11 進度；完成 HDLBits 的 Shift Registers。

## 遇到的困難與解決方案：
### Exams/2014 q4b
<img width="1214" height="844" alt="image" src="https://github.com/user-attachments/assets/351e30f6-f364-4a48-b2a9-2a7d25b969ca" />

1. 問題：
* **多重驅動衝突 (Multiple Drivers Error)**：
   * 在 `top_module` 中同時使用子模組 (`MUXDFF inst0(...)`) 與 `always @(posedge clk)` 區塊對相同的輸出埠 (`LEDR`) 進行指派。
   * **硬體觀念**：在數位電路中，一根導線 (Wire) 不能同時被兩個邏輯閘的輸出端驅動，否則會造成訊號衝突（短路/未知態 `X`）。
* **架構分工模糊 (Lack of Separation of Concerns)**：
   * 忽視了 `MUXDFF` 內部應包含 MUX 多路選擇器邏輯，導致頂層模組既做腳位串接又做邏輯判斷，失去模組化的意義。

2. 程式
```verilog
  	module top_module (
    input [3:0] SW,
    input [3:0] KEY,
    output [3:0] LEDR
); 
    
    
    MUXDFF inst0(
        .clk(KEY[0]),
        .q(LEDR[0])
    );
    MUXDFF inst1(
        .clk(KEY[0]),
        .q(LEDR[1])
    );
    MUXDFF inst2(
        .clk(KEY[0]),
        .q(LEDR[2])
    );
    MUXDFF inst3(
        .clk(KEY[0]),
        .q(LEDR[3])
    );
    
    always@(posedge KEY[0])begin
        case({KEY[1], KEY[2]})
            2'b00 : LEDR <= LEDR;
            2'b01 : LEDR <= SW;
            2'b10 : LEDR <= KEY[3];
            2'b11 : LEDR <= SW;
        endcase
    end

endmodule

module MUXDFF(
    input clk,
    input d,
    output q
);
    
    always@(posedge clk)begin
    	q <= d;
    end

endmodule
```

### Exams/ece241 2013 q12
<img width="1213" height="613" alt="image" src="https://github.com/user-attachments/assets/4888ad9d-2d7a-45af-9a8d-c95ea2f529e3" />

1. 問題：
* **多重驅動衝突 (Multiple Drivers Error)**：
   * 在頂層模組中同時實體化了 8 個 `dff8` (`inst0` ~ `inst7`)，且每一個子模組都將其 `Z` 腳位連接到頂層的 `output Z`。
   * **硬體觀念**：多個輸出端同時驅動同一根導線會造成訊號衝突（短路 / 未知態 `X`）。
     
* **時序與組合邏輯混淆 (Sequential vs. Combinational Misunderstanding)**：
   * 原設計將讀取選擇器（`case({A, B, C})`）寫在 `always @(posedge clk)` 的 `else` 分支內。
   * **問題所在**：
     * 當 `enable = 1`（進行移位寫入）時，`else` 不會執行，導致 `Z` **無法更新**。
     * LUT / RAM 的讀取動作（Random Access）本質上是 **組合邏輯 (Multiplexer)**，應該隨時反映 `{A, B, C}` 的位址變化，而不應該等待 `clk` 上升觸發或受 `enable` 控制。

2. 程式
```verilog
  	module top_module (
    input clk,
    input enable,
    input S,
    input A, B, C,
    output Z ); 
    
    dff8 inst0(
        .clk(clk),
        .enable(enable),
        .d(S),
        .A(A),
        .B(B),
        .C(C),
        .Z(Z) 
    );
    dff8 inst1(
        .clk(clk),
        .enable(enable),
        .A(A),
        .B(B),
        .C(C),
        .Z(Z) 
    );
    dff8 inst2(
        .clk(clk),
        .enable(enable),
        .A(A),
        .B(B),
        .C(C),
        .Z(Z) 
    );
    dff8 inst3(
        .clk(clk),
        .enable(enable),
        .A(A),
        .B(B),
        .C(C),
        .Z(Z) 
    );
    dff8 inst4(
        .clk(clk),
        .enable(enable),
        .A(A),
        .B(B),
        .C(C),
        .Z(Z) 
    );
    dff8 inst5(
        .clk(clk),
        .enable(enable),
        .A(A),
        .B(B),
        .C(C),
        .Z(Z) 
    );
    dff8 inst6(
        .clk(clk),
        .enable(enable),
        .A(A),
        .B(B),
        .C(C),
        .Z(Z) 
    );
    dff8 inst7(
        .clk(clk),
        .enable(enable),
        .A(A),
        .B(B),
        .C(C),
        .Z(Z) 
    );
    
    
endmodule

module dff8(
    input clk,
    input enable,
    input d,
    input A, B, C,
    output Z,
    output [7:0]q
);
    
    always@(posedge clk)begin
        if(enable)begin
            q[0] <= d;
            q[1] <= q[0];
            q[2] <= q[1];
            q[3] <= q[2];
            q[4] <= q[3];
            q[5] <= q[4];
            q[6] <= q[5];
            q[7] <= q[6];
        end
        else begin
            case({A, B, C})
                3'b000 : Z <= q[0];
                3'b001 : Z <= q[1];
                3'b010 : Z <= q[2];
                3'b011 : Z <= q[3];
                3'b100 : Z <= q[4];
                3'b101 : Z <= q[5];
                3'b110 : Z <= q[6];
                3'b111 : Z <= q[7];
            endcase
        end
    end
    
endmodule
```


### 解法：
### Exams/2014 q4b
1. **頂層模組 (`top_module`)**：專注於硬體連線 (Wiring/Instantiation)，**完全移除 `always` 區塊**，避免重複驅動 `LEDR`。
2. **子模組 (`MUXDFF`)**：將 MUX 選擇邏輯（`L` 判斷是載入 `R` 還是移位 `w`）與 D-Flip-Flop 包裹在子模組內部。

3. 程式碼
  ```verilog
  module top_module (
    input [3:0] SW,
    input [3:0] KEY,
    output [3:0] LEDR
	); //
    
	    MUXDFF inst0(
	        .clk(KEY[0]),
	        .E(KEY[1]),
	        .L(KEY[2]),
	        .w(LEDR[1]),
	        .R(SW[0]),
	        .q(LEDR[0])
	    );
	    
	    MUXDFF inst1(
	        .clk(KEY[0]),
	        .E(KEY[1]),
	        .L(KEY[2]),
	        .w(LEDR[2]),
	        .R(SW[1]),
	        .q(LEDR[1])
	    );
	    
	    MUXDFF inst2(
	        .clk(KEY[0]),
	        .E(KEY[1]),
	        .L(KEY[2]),
	        .w(LEDR[3]),
	        .R(SW[2]),
	        .q(LEDR[2])
	    );
	    
	    MUXDFF inst3(
	        .clk(KEY[0]),
	        .E(KEY[1]),
	        .L(KEY[2]),
	        .w(KEY[3]),
	        .R(SW[3]),
	        .q(LEDR[3])
	    );
	
	endmodule
	module MUXDFF (
    input clk,
    input w, E, L, R,
    output q
	);
    
    	always@(posedge clk)begin
        	case({E, L})
        		2'b00 : q <= q;
            	2'b01 : q <= R;
            	2'b10 : q <= w;
            	2'b11 : q <= R;
        	endcase
    	end

	endmodule
  ```

### Exams/ece241 2013 q12
1. **無需拆分 8 個子模組**：`top_module` 本身即包含一個 8-bit 的移位暫存器 (`reg [7:0] q`)。
2. **寫入邏輯（時序邏輯）**：當 `enable = 1` 時，在 `posedge clk` 將 `S` 移入 `q[0]`。
3. **讀取邏輯（組合邏輯）**：直接利用動態索引 `assign Z = q[{A, B, C}];` 實作 8-to-1 MUX，確保讀取獨立且無延遲。

5. 程式碼
  ```verilog
  module top_module (
    input clk,
    input enable,
    input S,
    input A, B, C,
    output Z
);

    reg [7:0] q;

    // 寫入部分：當 enable=1 時，進行 8-bit 移位 (Sequential Logic)
    always @(posedge clk) begin
        if (enable) begin
            // S 輸入給 Q[0]，其他位元向高位移（Q[0]->Q[1]->...->Q[7]）
            q <= {q[6:0], S}; 
        end
    end

    // 讀取部分：以 {A, B, C} 作為位址選出對應的 Q[i] (Combinational Logic)
    assign Z = q[{A, B, C}];

endmodule
  ```

## 關鍵知識/詞彙：
### 移位暫存器與 LFSR (Linear-Feedback Shift Register)

1. 核心觀念
* **Fibonacci vs. Galois 架構**：
  * **Fibonacci 型**：多個 Taps 經過多層 XOR 再回授至輸入端，組合邏輯延遲較高。
  * **Galois 型**：回授訊號（通常為 `q[0]`）直接拉出，並行插入到各 Tap 之間的 XOR 閘，**關鍵路徑僅經過 1 個 XOR 閘**，更易達到 Timing Closure。
* **位元運算技巧**：
  * **右移與回授**：`q <= {q[0], q[31:1]}` 實現了「將最高位填入回授 `q[0]`」與「其餘位元右移一階」。
  * **特定 Tap 更新**：在非阻塞指派 (`<=`) 下，可直接用 `q[tap] <= q[tap+1] ^ q[0];` 覆蓋特定位置。

2. 常見踩雷點
* **狀態鎖死 (Lockup)**：以 XOR 為基礎的 LFSR **絕對不能初始化為全 0**（全 0 經過 XOR 結果永遠為 0），重置時必須載入非零 Seed（如 `5'h1`）。
* **陣列越界 (Out of Bounds)**：使用 `for` 迴圈處理位元時，務必注意 0-based 索引（如 32-bit 的最高位為 `q[31]`，而非 `q[32]`）。

---

### 模組化設計與訊號驅動規則 (Submodules & Drivers)

1. 核心觀念
* **職責分離 (Separation of Concerns)**：
  * **子模組 (Submodule)**：專注於組合與時序邏輯運算（如包含 MUX + DFF）。
  * **頂層模組 (Top Module)**：專注於硬體結構串接 (Structural Modeling)，**儘量避免在頂層使用 `always` 區塊與子模組同時操作同一訊號**。

2. 常見踩雷點
* **多重驅動衝突 (Multiple Drivers Error)**：
  * **現象**：同一訊號（如 `LEDR` 或 `Z`）同時被 `always` 區塊與子模組（`inst0`）驅動，或是多個子模組輸出接到同一導線。
  * **硬體本質**：多個邏輯閘輸出直接短路，導致未知態 (`X`)。
* **`reg` 與 `wire` 宣告混淆**：
  * 在 `always` 區塊內指派的變數（如 `q <= d;`），其埠型態必須明確宣告為 **`output reg`**。

---

### 時序與組合邏輯的邊界 (Sequential vs. Combinational)

1. 核心觀念
* ** Look-Up Table (3-LUT / RAM) 設計**：
  * **寫入 (Write)**：屬於**時序邏輯 (Sequential Logic)**，需受時脈 `clk` 與 `enable` 控制（如 `q <= {q[6:0], S};`）。
  * **讀取 (Read)**：屬於**組合邏輯 (Combinational Logic)**，本質為 Multiplexer (MUX)，應獨立於 `clk` 與 `enable`，隨時反映位址輸入（如 `assign Z = q[{A, B, C}];`）。

2. 最佳實踐 (Best Practices)
* **動態索引即 MUX**：在 Verilog 中使用 `q[index]` 可以極簡且精確地被合成器轉譯為多路選擇器 (MUX)，無需撰寫冗長的 `case` 敘述。
* **拼接運算符 `{}`**：優先使用 `{}` 進行位元拼接與移位（如 `{q[6:0], S}`），比編寫 `for` 迴圈更具可讀性且不容易出現語法錯誤。


[回目錄](#toc)

---
<a id="m08d13"></a>

## 2026 年 8 月 13 日

## 今日進度：
### 資料：
1. [Digital Design and Computer Architecture(Spring 2025)](https://safari.ethz.ch/ddca/spring2025/doku.php?id=start)
2. [Digital Design and Computer Architecture, David Harris and Sarah Harris (Chapter 1-2)](https://www.sciencedirect.com/book/9780123704979/digital-design-and-computer-architecture)

### 影片：
1. [Digital Design and Computer Architecture(Spring 2025) L1 - L2](https://www.youtube.com/watch?v=ubhxKNlOlRg&list=PL5Q2soXY2Zi9Eo29LMgKVcaydS7V1zZW3)


## 關鍵知識/詞彙：
### 計算機系統抽象層級 (Layers of Abstraction)
<img width="438" height="585" alt="image" src="https://github.com/user-attachments/assets/891d39eb-f677-4c41-b5dd-72b94c6918f2" />

| 層級 | 英文名稱 | 範例與說明 |
| :--- | :--- | :--- |
| 頂層 | Problem | 實際欲解決的問題（如：AI 圖像識別） |
| | Algorithm | 解決問題的演算法（如：Quick Sort, CNN） |
| | Program/Language | 高階程式語言（如：C++, Python, Java） |
| | System Software | 作業系統、編譯器、組譯器（OS, Compilers） |
| 中介 | SW/HW Interface | 指令集架構 (ISA)：如 x86, ARM, RISC-V |
| | Micro-architecture | 處理器內部硬體實現（如：流水線、快取設計） |
| | Logic | 數位邏輯閘與電路（如：AND/OR/NAND、暫存器） |
| | Devices | 半導體元件（如：晶體管 Transistors, MOSFET） |
| 底層 | Electrons | 物理層與電子運動 |

* 關鍵概念：SW/HW Interface (ISA)：
	* 軟硬體的橋樑：介於系統軟體與底層硬體之間，是兩者溝通的標準協定。
	* 抽象化契約：定義了機器看得懂的指令集、暫存器（Registers）、記憶體定址模式與資料型態。軟體開發者只需針對 ISA 寫作/編譯，無需關心硬體底層電路的具體實現。
---
### 當前計算機架構的挑戰 (Computer Architecture Today)
<img width="512" height="380" alt="image" src="https://github.com/user-attachments/assets/92e0b2df-2d4a-4565-b9f7-0299f55773fd" />

* 現代計算機架構正經歷重大範式轉移（Paradigm Shift），主要推動因素與面對的難題如下：
	* 巨量數據需求 (Data Hunger)：AI 與數據密集型應用的快速崛起。
	* 功耗/散熱限制 (Power Constraints)：功耗牆限制了單核頻率的無限制提升。
	* 設計複雜度 (Design Complexity)：晶片規模與驗證難度大幅增加。
	* 技術微縮困難 (Technology Scaling)：摩爾定律（Moore's Law）與鄧納德縮放定律（Dennard Scaling）放緩。
	* 記憶體瓶頸 (Memory Bottleneck)：「記憶體牆」問題，即 CPU 算力遠快於 DRAM 存取速度。
	* 資安與硬體漏洞 (Security & Privacy)：如 Spectre、Meltdown 及 Rowhammer 攻擊。
---
### 硬體實現技術比較 (Hardware Platforms)
<img width="995" height="683" alt="image" src="https://github.com/user-attachments/assets/d794b7e9-79e9-421f-9ba2-5aee9b721454" />

| 比較項目 | 微處理器 (Microprocessors) | FPGA | ASIC |
| :--- | :--- | :--- | :--- |
| 特性 | 通用構件（CPU/GPU） | 可重新組態的硬體，極具彈性 | 完全客製化的專用晶片 |
| 開發時間 | 幾分鐘（撰寫軟體） | 幾天（邏輯合成與佈局） | 幾個月（實體光罩與下片） |
| 相對效能 | 基準 (o) | 較優 (+) | 極佳 (++) |
| 適用場景 | 通用計算、簡單易用 | 原型設計 (Prototyping)、小量生產 | 量產產品、追求極致效能與低功耗 |
| 產出形式 | 可執行檔 (Executable File) | 位元流檔案 (Bitstream) | 設計光罩 (Design Masks) |
| 程式語言 | C / C++ / Java / Python | Verilog / VHDL | Verilog / VHDL |
| 代表廠商 | Intel, AMD, ARM, Apple, NVIDIA | Xilinx (AMD), Altera (Intel) | TSMC (台積電), GlobalFoundries |
---
### CMOS 邏輯閘結構 (CMOS Logic Gates)
<img width="1000" height="749" alt="image" src="https://github.com/user-attachments/assets/1423d5e6-080b-4106-b5c1-04cf5e147478" />

1. 邏輯規則：
  * 電晶體並聯 (Parallel)：任意一個導通，網路即導通（對應 OR 邏輯）。
  * 電晶體串聯 (Series)：所有電晶體皆導通，網路才導通（對應 AND 邏輯）。

2. 特性：pMOS 與 nMOS 互補，在靜態時絕不同時導通，因此靜態功耗極低，且預設輸出為反相特性（如 NOT, NAND, NOR）。

3. 變型結構：Pseudo-nMOS 
<img width="186" height="193" alt="image" src="https://github.com/user-attachments/assets/e18859f7-16e6-4e07-8009-ee0036ad1335" />

* 為了減少晶片面積與電晶體數量，將上拉網路替換為單一常開的弱 pMOS（Weak pMOS）：
	* 優點：N 個輸入只需 N + 1 個電晶體（標準 CMOS 需要 2N 個），大幅縮減面積與輸入電容。
	* 缺點：當輸出為邏輯 0 時，pMOS 與 nMOS 同時導通，會產生持續的靜態功耗 (Static Power)；且輸出低電位準（VOL）無法完美降至 0V，雜訊邊限較差。
---
### 功耗與能量消耗 (Power & Energy Consumption)
<img width="999" height="749" alt="image" src="https://github.com/user-attachments/assets/aaefda82-5d66-4b78-a3d3-69955b87500c" />

1. 動態功耗 (Dynamic Power)
* 電路在訊號切換（0 <-> 1）對電容充放電時產生的功耗。

$$P_{\text{dynamic}} = C \cdot V^2 \cdot f$$

* C：電路的負載電容（包含連線與邏輯閘電容）
* V：工作電壓（降低電壓可帶來二次方的省電效果）
* f：電容充放電的切換頻率

2. 靜態功耗 (Static Power)
電路靜止未切換時，因半導體漏電流（Leakage Current）所產生的功耗。

$$P_{\text{static}} = V \cdot I_{\text{leakage}}$$

* I_leakage：漏電流（隨製程微縮而持續增加）

3. 總能量消耗 (Energy Consumption)
執行特定工作負載所消耗的總能量：

$$\text{Energy} = \text{Power} \times \text{Time}$$



[回目錄](#toc)

---
