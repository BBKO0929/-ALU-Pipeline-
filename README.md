# 32-bit 的加法器/運算單元（ALU）Pipeline-MAC-Accelerator

本專案旨在基於硬體流水線（Pipeline）架構，設計一款兼具高效能與面積效益的 32 位元運算單元。

## 開發進度與自主學習日誌

- **[點我觀看完整的「每日學習與 Debug 日誌」](./learning_log.md)**
- **[點我觀看完整的「專題製作過程與 Debug 日誌」](./32-bit_ALU_log.md)**
- **[點我觀看「參考文獻」](./References.md)**
------------------------------

## 核心亮點與 PPA 性能對比 (Performance & Trade-off)

針對 Baseline（純組合邏輯）與 Pipeline（2-Stage 流水線）版本進行 STA 靜態時序分析對比，結果如下：

| 項目 | Baseline（alu_v1） | Pipeline（alu_v2, 2-stage） |
|---|---|---|
| Period（收斂值） | 7.2 ns | 4.5 ns |
| WNS | 0.516 ns | 0.551 ns |
| Fmax | ≈ 149.6 MHz | ≈ 253.2 MHz |
| Data Path Delay(關鍵路徑實際延遲) | 6.710ns | 3.813ns |
| LUT | 327 | 317 |
| FF | 103 | 172 |
| Latency | 1 cycle | 2 cycle |

**核心要點：**
1. Baseline 版本是純組合邏輯，加法器用 Ripple Carry Adder，關鍵路徑是進位一路傳到最高位，限制了整體時脈上限，收斂後 Fmax 約 149.6MHz(對應關鍵路徑實際延遲 6.710ns)。
   
2. Pipeline 版本把資料路徑切成 2 個 stage，中間插入暫存器，把原本一次算完的長路徑拆成兩段較短的路徑，單一 stage 的關鍵路徑延遲由 6.710ns 降至 3.813ns，收斂後 Fmax 約 253.2MHz，相較 baseline 提升約 1.69 倍。
   
3. trade-off:
	* 用「暫存器(FF)數量換取頻率/吞吐量」:FF 用量從 103 增加到 172(多了約 69 顆暫存器,用於 Stage1/Stage2 之間鎖存中繼資料)
	* LUT 用量反而略降(327→317)：因為 Baseline 中 ADD/SUB、邏輯運算、移位、SLT 四種結果各自需要獨立的輸出多工器(4-to-1 MUX)依 op_code 選擇；Pipeline 版本因為 Stage1 已提前算出並鎖存 add_sub 結果，Stage2 的 final_res 選擇邏輯改為直接從已鎖存的中間值中選取，讓 Synthesizer 得以合併重複的選擇邏輯，因此 LUT 不增反減——資源增加主要來自新增的暫存器，而非額外的運算邏輯。
	* Latency 則從 1 cycle 增加為 2 cycle，犧牲單筆資料的延遲，換取整體吞吐量(throughput)與可運作頻率的提升

4. 兩個版本的功能都用同一組 testbench 驗證過，結果完全等價，確保這組時序數據的比較是建立在功能正確、公平的基準。
---

## 架構設計與流水線切割策略 (Microarchitecture)
<img width="544" height="316" alt="image" src="https://github.com/user-attachments/assets/edfdfcaf-8039-4a88-8e76-640d7a6a100d" />
<img width="923" height="507" alt="image" src="https://github.com/user-attachments/assets/24a57abe-c425-42a0-8ac1-af8253aafec1" />


### 1. 在 alu_v1 中的 critical path

* Barrel Shifter + Zero Flag（最主要的 Critical Path，實際终點是 flag_out_reg[3]，而非 res_out）
  * 邏輯結構：採用 5 階串聯的條件選擇器（b[0] ~ b[4]）對應 1, 2, 4, 8, 16 bits 移位，
    移位結果再經過 32-bit NOR Reduction 判斷 Zero Flag。
  * 延遲原因：訊號必須連續穿過 5 個 32-bit MUX，每一個 MUX 的 Gate Delay 會線性累加；
    移位結果出來後，還要再疊加一段 32-bit NOR Reduction 樹才能鎖入 flag_out，
    兩段邏輯相加才是全模組最長的邏輯鏈（實測 Data Path Delay = 6.710ns，對應 flag_out_reg[3]）。
  * 補充：走到 res_out 的路徑（純移位、不含 Flag 判斷）Slack 還有 1.4ns 以上餘裕，
    並非真正瓶頸，代表 Zero Flag 的判斷邏輯才是壓垮 WNS 的最後一根稻草。

* 33-bit Add/Sub（加減法器）：次要瓶頸
  * 邏輯結構：assign add_sub = {1'b0, a} + {1'b0, b_op} + sub;
  * 延遲原因：高位元（MSB）必須等待低位元（LSB）一路傳遞上來的進位訊號（Carry Chain），
    需等待 32 個 Full Adder 的傳遞延遲。

* 位元邏輯運算（AND / OR / XOR）：非瓶頸
  * 邏輯結構：32-bit 平行運算。
  * 延遲原因：僅需經過 1 階邏輯門，無位元間的依賴關係，延遲極短。

* SLT（Set Less Than）：非瓶頸
  * 邏輯結構：直接沿用 33-bit Add/Sub 運算出的最高位元（符號位）與 Overflow 判斷結果，
    不需額外的比較運算路徑。
  * 延遲原因：與 Flag 產生邏輯共用同一組加法器輸出，延遲可忽略，
    不會拖累整體 critical path。

### 2. 流水線（Pipelining）切割策略
<img width="581" height="648" alt="image" src="https://github.com/user-attachments/assets/084ded80-5b4e-479d-ac07-4312b5f50bf4" />

   
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

   * Pipeline 化後 Zero Flag 不再是瓶頸
      * Baseline 中，Zero Flag 需等移位器 5 級 MUX 全部跑完後，再疊加一層 32-bit NOR Reduction，是實測中真正決定 WNS 的路徑。
      * Pipeline 化後，因為切成兩級，Stage 2 只需處理後 2 級 MUX（8, 16 bits）加上收斂後的 Flag/最終選擇邏輯，NOR Reduction 疊加在較短的路徑上，不再是全模組最長路徑；實測顯示 V2 的最長路徑（Path 1）反而落在 res_reg 的移位輸出，而非 flag_out——代表 pipeline 切割同時解決了「移位延遲」與「Flag 延遲疊加」兩個問題，而不只是單純把加法器和移位器分開。

* **Timing Balance (時序平衡)**： Stage 1（前 3 階 Mux + 加法器傳遞）與 Stage 2（後 2 階 Mux + Mux 選通 + Flag 判斷）的閘延遲幾乎完全相當，成功達到時序平衡。

### 3. 驗證與硬體除錯實務 (Verification & Debugging)

### Self-Checking Testbench 設計
* 涵蓋 ADD/SUB, AND/OR/XOR, SRA 符號延伸、ADD Overflow 邊界案例、SLT 有號數比較 與 Shamt 極值 (0, 31)。
* 使用 task automatic check 實現自動比對與錯誤計數。
* ex. ALU_V2（Pipeline） simulation sources
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

### 關鍵 Debug 經驗與原理
* Verilog Event Queue Race Condition 解決：
  * 問題： Testbench 與 RTL 同步在 posedge clk 採樣，由於非阻塞賦值（NBA）在同點更新，導致讀取到舊值（Race condition）。
  * 解法： 在比對前加入 #1 時間延遲（@(posedge clk); #1;），確保讀值時 NBA 已真正落地（Settled）。
    ```verilog
    @(posedge clk); // 第1拍：鎖進 Stage1
    @(posedge clk); // 第2拍：鎖進 Stage2
    #1; // 確保讀值時 NBA（非阻塞賦值） 已經真正落地
    ```

* 純組合邏輯 STA 靜態時序分析（Wrapper 設計）：
  * 問題： Baseline 版本缺少 Clock，無法建立 Register-to-Register Timing Path。
  * 解法： 包裹一層 alu_v1_wrapper 加入輸入/輸出暫存器，確立 Timing Path 進行精準 STA。
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

### 32bit_ALU_V1（Baseline） - Synthesis + Implementation 結果，並記錄收斂到 WNS 接近 0 的結果

* ALU_V1（Baseline）最終基準線數據
<img width="214" height="251" alt="image" src="https://github.com/user-attachments/assets/806079cd-4cc0-4a13-b4b7-5297b3a1180a" />
<img width="1327" height="99" alt="image" src="https://github.com/user-attachments/assets/a5696367-fd6b-4b48-9b5b-79145aaacbf5" />

* Period = 7.2ns, WNS = 0.516ns
* Fmax ≈ 149.6 MHz
* LUT = 327, FF = 103

### 32bit_ALU_V2（pipeline） - Synthesis + Implementation 結果，並記錄收斂到 WNS 接近 0 的結果
* ALU_V2（pipeline）最終基準線數據
<img width="213" height="257" alt="image" src="https://github.com/user-attachments/assets/e6e79d99-8720-451a-ae4d-08f9bbb5e3b7" />
<img width="1334" height="90" alt="image" src="https://github.com/user-attachments/assets/2dbb2fc8-a190-44de-8fe9-cfb091fc207f" />

* Period = 4.5 ns, WNS = 0.551 ns
* Fmax ≈ 253.2 MHz
* LUT = 317, FF = 172

### 4. 未來實體布局優化方向 (Physical Design & Fanout)
<img width="1062" height="387" alt="image" src="https://github.com/user-attachments/assets/6beb2c5e-173a-4285-907e-b0ef765f42f5" />

* 根據 Vivado Implementation Timing Report 分析：
  * Net Delay 遠大於 Logic Delay（如 Net: 2.611ns vs Logic: 1.202ns），控制訊號高扇出（High Fanout = 16~32）為最後瓶頸。

* 下一步優化提案： 針對 Stage 1 的 op_code 採用 One-hot 提前解碼，並下達 MAX_FANOUT 約束促進工具進行暫存器複製（Register Replication），進一步削減 Net Delay。
