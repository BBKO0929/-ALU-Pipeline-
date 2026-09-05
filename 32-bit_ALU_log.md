# 32-bit ALU 製作過程與Debug日誌
- **[點我回「README.md」](./README.md)**


<a id="toc"></a>

## 目錄（點日期跳轉）

| 日期 | 內容摘要 |
|---|---|
| [7/27](#m07d27) | 32bit_ALU_V1（Baseline）- 設計初步 32-bit 構造、模型以及所支援的運算 |
| [7/28](#m07d28) | 32bit_ALU_V1（Baseline）- 完成 32-bit ALU baseline 版本與 debug |
| [8/1](#m08d01) | 32bit_ALU_V1（Baseline） - 改寫 32-bit ALU baseline 版本 testbench |
| [8/2](#m08d02) | 32bit_ALU_V1（Baseline） - 第一次 Synthesis + Implementation 結果，並記錄收斂到 WNS 接近 0 的結果 |
| [8/3](#m08d03) | ALU_V2（pipeline）設計：完成 RTL，共歷經 5 輪修正才達到邏輯正確 |
| [8/4](#m08d04) | 完成 Testbench - alu_v2_tt，驗證 pipeline 版本功能，經多輪除錯後全數 PASS |
| [8/5](#m08d05) | 32bit_ALU_V2（pipeline） - 第一次 Synthesis + Implementation 結果，並記錄收斂到 WNS 接近 0 的結果 |
| [8/11](#m08d11) | 32bit_ALU_V2（pipeline） - 主動重構，把邏輯運算與 SLT 先獨立拆成一個組合邏輯 ，Stage1 暫存器改成**每拍無條件更新** |
| [8/17](#m08d17) | 32bit_ALU_V2（pipeline） - 優化 Stage1 暫存器位元寬度精簡、SLT 共用加減法器硬體 |

---

<a id="m07d27"></a>

## 2026 年 7 月 27 日


## 今日進度：
### 資料：
1. [Barrel Shifters in Verilog: A Beginner's Guide to Fast Multi-Bit Shifting](https://medium.com/@ahe24mobile/barrel-shifters-in-verilog-a-beginners-guide-to-fast-multi-bit-shifting-121d1c5a2b62)
2. [How to Design an Efficient Barrel Shifter in Verilog: Step-by-Step Guide](https://vlsifacts.com/how-to-design-an-efficient-barrel-shifter-in-verilog-step-by-step-guide/)

## 今日成果探討：
### 32bit_ALU_V1（Baseline）- 設計初步 32-bit 構造、模型以及所支援的運算
<img width="544" height="316" alt="image" src="https://github.com/user-attachments/assets/edfdfcaf-8039-4a88-8e76-640d7a6a100d" />
<img width="923" height="507" alt="image" src="https://github.com/user-attachments/assets/24a57abe-c425-42a0-8ac1-af8253aafec1" />

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
* 專門驗證 overflow 判斷式 `(~(a[31] ^ b_op[31])) && (a[31] ^ add_sub[31])`（兩運算元同號、結果卻異號）有沒有正確抓到這種情況，一般隨便挑的數值很難自然踩到這個邊界

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
* Period = 7.2ns, WNS = 0.516ns
* Fmax ≈ 149.6 MHz
* Data Path Delay(關鍵路徑實際延遲)： 6.710ns
* LUT = 327, FF = 103
<img width="1703" height="214" alt="image" src="https://github.com/user-attachments/assets/f049cacc-aa64-4e97-86c7-ec478c4f5276" />


### ALU_V2（pipeline） - pipeline ALU 設計規劃
<img width="581" height="648" alt="image" src="https://github.com/user-attachments/assets/084ded80-5b4e-479d-ac07-4312b5f50bf4" />

1. 在 alu_v1 中的 critical path
   * Barrel Shifter（桶型移位器）：最主要的 Critical Path
     * 邏輯結構： 採用 5 階串聯的條件選擇器（`b[0]` ~ `b[4]` 對應 1, 2, 4, 8, 16 bits 移位）。
	 * 延遲原因： 訊號必須**連續穿過 5 個 32-bit MUX**。每一個 MUX 的 Gate Delay 會**線性累加**，形成全模組最長的邏輯鏈（Logic Chain）。

   * 33-bit Add/Sub（加減法器）：次要瓶頸
	 * 邏輯結構： `assign add_sub = {1'b0, a} + {1'b0, b_op} + sub;`
	 * 延遲原因： 高位元（MSB）必須等待低位元（LSB）一路傳遞上來的**進位訊號（Carry Chain）**，需等待 32 個 Full Adder 的傳遞延遲。

   * 位元邏輯運算（AND / OR / XOR）：非瓶頸
	 * 邏輯結構： 32-bit 平行運算。
	 * 延遲原因： 僅需經過 **1 階邏輯門**，無位元間的依賴關係，延遲極短。

2. 流水線（Pipelining）切割策略
   * 為什麼不採用 16-bit / 16-bit 高低位拆分？
   	 * **加法器 Carry 依賴：** 高 16-bit 必須等待低 16-bit 的 Carry Out，橫向拆分無法打破時間依賴。
     * **移位器位元跨界：** 桶型移位器的資料會在 32-bit 空間內跨界移動，拆成高低 16-bit 會破壞移位邏輯。

   * 2-Stage Pipeline 切割方案 (縱向階段切割)
     * Stage 1：Shifter 前 3 階 (b[0]~b[2]: 1, 2, 4 bits 移位) ； 33-bit 加減法運算 (ADD / SUB / SLT)
     * Stage 2：Shifter 後 2 階 (b[3]~b[4]: 8, 16 bits 移位)；Flag 生成 (Z, N, C, V)

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
* 原因：控制訊號跟資料時間點不一致，跟問題4是同一類問題
* 解法：Stage1 額外鎖存 `sub_stg1`，Stage2 改用 `b_op_stg1 = b_stg1 ^ {32{sub_stg1}}`

### 問題6：ADD / SUB 的 flag 讀錯來源（反覆出現在第1、2版，第3版才真正解決）
* 原因：ADD/SUB 的真正結果存在 `add_sub_stg1`，但 flag 判斷 Z/N 時卻讀取 `res_stg1`（該次 case 沒有對應分支，值是殘留的舊值），導致 ADD/SUB 的 Z/N flag 完全錯誤
* 解法：在 Stage2 用組合邏輯統一算出 `final_res`（依 op_code_stg1 選出這一拍真正的最終結果，ADD/SUB 選 add_sub_stg1，其餘選對應暫存器），res 跟 flag 的 Z/N 都從 final_res 取值，不再各自各的

### 問題7：flag 只有 ADD / SUB 才更新，其他運算 flag 維持舊值不動
* 原因：整個 flag（含 Z/N）都包在 `if(ADD||SUB)` 裡面才更新，跟 baseline 行為（Z/N 每種運算都要算，只有 C/V 限定 ADD/SUB）不一致
* 解法：拿掉 Z/N 的 if 限制，改成永遠算 Z/N，只有 C/V 用三元運算子限定 ADD/SUB 才給實際值、否則補 0

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
* Data Path Delay(關鍵路徑實際延遲)： 3.813ns
<img width="1062" height="387" alt="image" src="https://github.com/user-attachments/assets/6beb2c5e-173a-4285-907e-b0ef765f42f5" />

### Baseline vs Pipeline 最終對照表

| 項目 | Baseline（alu_v1） | Pipeline（alu_v2, 2-stage） |
|---|---|---|
| Period（收斂值） | 7.2 ns | 4.5 ns |
| WNS | 0.516 ns | 0.551 ns |
| Fmax | ≈ 149.6 MHz | ≈ 253.2 MHz |
| Data Path Delay(關鍵路徑實際延遲) | 6.710ns | 3.813ns |
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
<a id="m08d11"></a>

## 2026 年 8 月 11 日

## 今日成果探討：
### ALU 設計優化：
### 32bit_ALU_V2（pipeline） - 主動重構，把邏輯運算與 SLT 先獨立拆成一個組合邏輯 `res_com1`（`always@(*)`），Stage1 暫存器改成**每拍無條件更新**

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
* Fmax ≈ (4.5-0.473) = 4.027ns → Fmax ≈ 248.3 MHz
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

2. 邏輯運算單元優化：SLT (Set Less Than) 共享加減法器結果

* **原設計瓶頸 (Baseline Bottleneck)：**
  在 Stage 1 中使用 `$signed(a) < $signed(b)` 進行有號數比較，會導致綜合工具（Synthesis Tool）額外合成出一組獨立的 32-bit 符號比較器（Signed Comparator），增加不必要的晶片面積與 Critical Path 延遲。

* **優化策略 (Optimization Strategy)：**
  利用補數算術邏輯，有號數比較 $A < B$ 在數學上完全等同於判斷 $A - B$ 結果的**負號（Sign Bit, $S_{31}$）與溢位（Overflow, $V$）**。SLT 運算可直接移至 Stage 2，共享加減法器的運算結果：

  $$\text{SLT} = S_{31} \oplus V$$

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
<a id="m08d17"></a>

## 2026 年 8 月 17 日

## 今日成果探討：
### ALU 設計優化：
### 32bit_ALU_V2（pipeline） - 優化 Stage1 暫存器位元寬度精簡、SLT 共用加減法器硬體

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

    assign sub = (op_code == 3'b001) || (op_code == 3'b111); // op_code == 3'b001 or 3'b111，執行減法
    assign b_op = b ^ {32{sub}}; // 將 b 逐位元反相
    assign add_sub = {1'b0, a} + {1'b0, b_op} + sub; // ADD: a+b；SUB: a + ~b + 1（2補數，+1 由 sub 提供）
    
    // b[0] ~ b[2] 的 logic_shift_left
    assign s1_l = (b[0]) ? {a[30:0], 1'b0} : a;
    assign s2_l = (b[1]) ? {s1_l[29:0], 2'b0} : s1_l;
    assign s3_l = (b[2]) ? {s2_l[27:0], 4'b0} : s2_l;
    
    // b[0] ~ b[2] 的 arithmetic_shift_right
    assign s1_r = (b[0]) ? {a[31], a[31:1]} : a;
    assign s2_r = (b[1]) ? {{2{a[31]}}, s1_r[31:2]} : s1_r;
    assign s3_r = (b[2]) ? {{4{a[31]}}, s2_r[31:4]} : s2_r;
    
    // 構建 stage1 -> stage2 中間暫存器
    reg a_stg1;
    reg [1:0]b_stg1;
    reg [2:0]op_code_stg1;
    reg [32:0]add_sub_stg1;
    reg b_op_stg1;
    reg [31:0]shift_stg_l;
    reg [31:0]shift_stg_r;
    reg [31:0]res_stg1;
    
    // 邏輯運算與 SLT 先獨立拆成一個組合邏輯
    reg [31:0]res_com1;
    always@(*)begin
        case(op_code)
            3'b010 : res_com1 = a & b;
            3'b011 : res_com1 = a | b;
            3'b100 : res_com1 = a ^ b;
            3'b111 : res_com1 = 0; // SLT 運算改為 STAGE 2 判斷，為完全共享加減法器（Adder/Subtractor）的運算路徑
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
            a_stg1 <= a[31]; // a 在 Stage 2 只用到最高位元 1 bit
            b_stg1 <= b[4:3]; // shamt[b] 在 Stage 2 只用計算 b[3] ~ b[4] 移位
            b_op_stg1 <= b_op[31]; // b_op 在 Stage 2 只需最高位元來判斷溢位
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
    //Shamt 選用 b_stg1[0] ~ b_stg1[1] 判斷移動幾個 bit
    wire [31:0]s4_l, shift_l_res;
    wire [31:0]s4_r, shift_r_res;
    
    
    // b_stg1[0] ~ b_stg1[1] 的 logic_shift_left
    assign s4_l = (b_stg1[0]) ? {shift_stg_l[23:0], 8'b0} : shift_stg_l;
    assign shift_l_res = (b_stg1[1]) ? {s4_l[15:0], 16'b0} : s4_l;
    // b_stg1[0] ~ b_stg1[1] 的 arithmetic_shift_right
    assign s4_r = (b_stg1[0]) ? {{8{a_stg1}}, shift_stg_r[31:8]} : shift_stg_r;
    assign shift_r_res = (b_stg1[1]) ? {{16{a_stg1}}, s4_r[31:16]} : s4_r;
    
    
    // 構建進位與溢位判斷
    wire carry_stg1;
    wire overflow_stg1;
    assign carry_stg1 = add_sub_stg1[32]; //進位判斷 
    assign overflow_stg1 = (~(a_stg1 ^ b_op_stg1)) && (a_stg1 ^ add_sub_stg1[31]); //溢位判斷
    
    // SLT 判斷
    wire SLT_res;
    assign SLT_res = overflow_stg1 ^ add_sub_stg1[31];
    
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
            3'b111 : final_res = {31'b0, SLT_res}; // SLT 運算
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
<img width="306" height="314" alt="image" src="https://github.com/user-attachments/assets/7b15f49b-063e-4dda-b360-059176e651eb" />
<img width="1568" height="77" alt="image" src="https://github.com/user-attachments/assets/7304a98e-4d30-4429-90fc-4f7667b64a6b" />
<img width="1568" height="355" alt="image" src="https://github.com/user-attachments/assets/6bf017d9-255a-4922-bef0-924ad69fc19e" />

---

### ALU_V2（Pipeline）基準線數據
* Period = 4.5 ns, WNS = 0.362 ns
* Fmax ≈ (4.5-0.362) = 4.138ns → Fmax ≈ 241.66 MHz
* LUT = 379, FF = 171

---
### 改動亮點與優化
1. **暫存器省面積 (Register Minimization)**：
   * 跨 Stage 暫存器精簡處理：$A$ 與 $B_{op}$ 在 Stage 2 僅需符號位，故僅鎖存 1-bit（`a_stg1` 與 `b_op_stg1`）
   * 效果：讓程式碼意圖更明確（一看就知道 Stage2 只需要這幾個 bit），提升可讀性
    
2. **1-bit 位元運算**：
   * 溢位判斷 `overflow_stg1` 與 `SLT_res` 全程使用 1-bit 邏輯進行運算，徹底避免 Verilog 隱式位元擴充（Implicit Expansion）導致的邏輯錯誤

3.讓 SLT 也走 `a - b` 這條路徑，跟 SUB 共用同一顆加法器
   * 效果：SLT 不再需要獨立的比較邏輯，理論上可以共用加法器硬體降低面積


# 32-bit ALU Pipeline 專題總結

## 專題概述
* 本專題設計並實作一顆支援 8 種運算（ADD/SUB/AND/OR/XOR/SLL/SRA/SLT）的 32-bit ALU，透過 Baseline（純組合邏輯）與 Pipeline（2-stage）兩個版本的對照，展示如何用「空間換取時間」的設計手法優化電路工作頻率，並在過程中針對移位器架構、加法器資源共用、控制訊號扇出等問題進行多輪迭代優化。

## 最終版本數據對照

| 項目 | Baseline（alu_v1） | Pipeline（alu_v2，最終版） |
|---|---|---|
| 架構 | 純組合邏輯 | 2-stage pipeline |
| Period（收斂值） | 7.2 ns | 4.5 ns |
| WNS | 0.516 ns | 0.473 ns |
| Fmax | ≈ 149.6 MHz | ≈ 248.3 MHz |
| LUT | 327 | 392 |
| FF | 103 | 171 |
| Latency | 1 cycle | 2 cycle |

* Fmax 提升約 **1.66 倍**（149.6MHz → 248.3MHz），這是本專題最核心的量化成果：透過在關鍵路徑中間插入暫存器，把原本一次算完的長邏輯鏈拆成兩段較短的路徑，讓電路能在更高的時脈下運作，代價是多了 1 cycle 的延遲，以及暫存器資源（FF：103→171）的增加，這是 pipeline 設計典型的 latency/throughput 與 area/speed trade-off。

## 設計與優化歷程

1. **Baseline 設計**：
   * 純組合邏輯 ALU，加法/減法共用同一顆 33-bit 加法器（2 補數技巧），barrel shifter 支援可變移位量（`shamt = b[4:0]`），並補上完整的 C/Z/N/V 四個狀態旗標。

2. **Pipeline 切割**：
   * 分析 baseline 的 critical path 後，確認 barrel shifter（5 級 MUX）與 33-bit 加法器是主要瓶頸，依「時間延遲均等平分」原則切成 2-stage：Stage1 負責前 3 級移位＋加減法，Stage2 負責後 2 級移位＋flag 產生。
     
3. **多輪除錯**：
   * 切 pipeline 過程中反覆修正資料流未串接、控制訊號時間點不同步（例如移位控制位元與資料未同拍鎖存）、flag 讀值來源錯誤等問題，並用同一組 corner case testbench 驗證每個版本與 baseline 功能等價。
     
4. **時序收斂**：
   * 透過二分法逐步調整 XDC 的 clock period，收斂找出 baseline 與 pipeline 各自的真實 Fmax，而非只用單一寬鬆設定的數字。
     
5. **瓶頸再分析與優化嘗試**：
    * 從 Timing Report 的 Net Delay／Logic Delay 比例與 High Fanout 欄位，定位出真正瓶頸是 `op_code_stg1` 控制訊號扇出過大（High Fanout 27~31）造成的繞線延遲，而非邏輯層數過深；並嘗試讓 SLT 運算共用加法器硬體（用 `overflow XOR sign` 判斷取代獨立比較器），同時精簡 Stage1 暫存器位元寬度，只保留 Stage2 真正需要的位元。

## 結果檢視：優化不一定總是往預期方向走

* SLT 共用加法器＋位寬精簡這輪優化，是本專題中特別值得記錄的一段：**理論上**共用硬體、精簡位寬應該要讓 LUT 用量下降，但實測結果 LUT 反而從 317 增加到 392，Fmax 也從 253.2MHz 略降到 248.3MHz。推測原因是：

	- `sub` 訊號多了一個判斷條件（`op_code==001 || op_code==111`），SLT 的 `overflow XOR sign` 判斷邏輯也是額外增加的組合邏輯，這些疊加起來可能抵銷了共用加法器省下的資源

	- 位元寬度精簡（`a_stg1`、`b_op_stg1` 從 32-bit 縮成 1-bit）主要提升的是程式碼可讀性，FF 用量幾乎沒變（172→171），因為 Vivado 本來就會自動裁剪掉未被下游使用的暫存器位元，並不一定會反映在實際合成結果上

## 驗證方法
兩個版本皆使用同一組手動計算 golden value 的 corner case testbench（涵蓋 8 種運算、ADD overflow 邊界、SRA 符號延伸、有號數 SLT 比較、shamt 邊界值 0 與 31），確保每一輪 RTL 修改後功能仍與設計預期一致，時序數據也是建立在功能正確的前提下才具有比較意義。



[回目錄](#toc)

---
