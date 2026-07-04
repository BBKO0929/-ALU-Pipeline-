# 2026 年 7 月 3 日
## 今日進度：
### 影片：看 TT 小教室第 1~5 課。
### 刷題：完成 HDLBits 的 "8-bit wide shift register of length 3"。

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

## 遇到的困難與解決方案：
### 問題：在寫always、case語法撰寫不完整，導致編譯錯誤。
### 解法：於程式後面加上end、endcase，成功編譯。
```verilog
always @(*) begin
    case()

    endcase
end
```
---------------------------------------------
# 2026 年 7 月 3 日
## 今日進度：
### 影片：看 TT 小教室第 1~5 課。
### 刷題：完成 HDLBits 的 "8-bit wide shift register of length 3"。

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

## 遇到的困難與解決方案：
### 問題：在寫always、case語法撰寫不完整，導致編譯錯誤。
### 解法：於程式後面加上end、endcase，成功編譯。
```verilog
always @(*) begin
    case()

    endcase
end
```
