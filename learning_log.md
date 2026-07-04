# 2026 年 7 月 3 日
## 今日進度：
### 影片：看 TT 小教室第 1~5 課。
### 刷題：完成 HDLBits 的 "basics" 到 "8-bit wide shift register of length 3(Three module)"。

## 遇到的困難與解決方案：
### 問題：在寫語法撰寫不完整，導致編譯錯誤。
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
