# 硬體設計理論、Verilog 規範與 EDA 工具文獻依據 (Theoretical Foundations & References)

本專案在 32-bit ALU 及 2-Stage Pipeline 運算單元之設計、靜態時序分析（STA）與硬體除錯過程中，所採用的關鍵技術與 Debug 判斷依據，皆遵循以下學術文獻、國際標準規範與工業界 EDA 官方指南。

---

## 1. 計算機結構與算術邏輯 (Computer Architecture & Arithmetic)

### 桶型移位器 (Barrel Shifter) 與平滑縱向切割
* **參考文獻：**
  * Behrooz Parhami, *Computer Arithmetic: Algorithms and Hardware Designs*, Oxford University Press.
  * A. He, "Barrel Shifters in Verilog: A Beginner's Guide to Fast Multi-Bit Shifting," *Medium*.
  * VLSI Facts, "How to Design an Efficient Barrel Shifter in Verilog: Step-by-Step Guide."

### 2 補數加減法與 Flag (Carry / Overflow) 判斷
* **參考文獻：**
  * David A. Patterson and John L. Hennessy, *Computer Organization and Design: The Hardware/Software Interface* (RISC-V / MIPS Editions), Morgan Kaufmann.

---

## 2. Hardware Description Language (IEEE Verilog HDL 規範)

### Event Queue Scheduling 與 Race Condition 排除
* **除錯依據：** 在 Testbench 中，若於 `@(posedge clk)` 同一時脈邊緣直接進行 `blocking (=)` 取樣比對，會與 RTL DUT 的 `non-blocking (<=)` 發生競爭條件（Race Condition），讀取到更新前的舊值。在比對前加入 `#1` 延遲，能確保模擬器時間軸跨過 Active Event Region 並進入 Non-blocking Assign (NBA) Update Region，正確讀取已落地的暫存器數值。
* **標準規範：**
  * **IEEE Std 1364-2005**, *IEEE Standard for Verilog Hardware Description Language*, Section 5: "Scheduling semantics" (Event-driven simulation semantics & NBA region).

### `$signed()` 系統函式行為機制
* **除錯依據：** `$signed()` 僅為「類型重新解讀（Type Reinterpretation）」，不會改變底層任何 Bit pattern，亦無法提供額外的 Bit 寬度用於捕捉進位（Carry Out）。因此減法與進位需透過顯示擴展至 33-bit（`{1'b0, a}`）進行運算。
* **標準規範：**
  * **IEEE Std 1364-2005**, *IEEE Standard for Verilog Hardware Description Language*, Section 4.5: "Signed expressions".

---

## 3. 靜態時序分析與實體布局優化 (STA & Physical Design)

### 純組合邏輯 Wrapper 設計與 Timing Path 建立
* **除錯依據：** 靜態時序分析工具（如 Vivado Timing Engine）必須建立起「Reg-to-Reg（暫存器至暫存器）」的完整 Timing Path，方能精準計算 Setup/Hold Time 與 Worst Negative Slack (WNS)。因此 Baseline 純組合邏輯必須包裹一層輸入/輸出暫存器（Wrapper）進行時序收斂驗證。
* **官方指南：**
  * **AMD / Xilinx UG906**, *Vivado Design Suite User Guide: Design Analysis and Closure Techniques*, Chapter 2: "Timing Analysis Concepts".

### High Fanout 扇出抑制與訊號複製 (`_replica`)
* **除錯依據：** 當 Timing Report 顯示 Net Delay 遠大於 Logic Delay（如 Net 2.611 ns vs Logic 1.202 ns）且控制訊號驅動數過高（Fanout 16~32）時，代表實體繞線為主要瓶頸。優化策略包括：
  1. 在 RTL 採用 One-hot 提前解碼分散下游邏輯驅動負載。
  2. 使用 `MAX_FANOUT` 屬性約束工具，觸發自動暫存器複製（Register Replication，產生 `_replica` 暫存器）以降低實體連線負載。
* **官方指南：**
  * **AMD / Xilinx UG901**, *Vivado Design Suite User Guide: Synthesis*, Section: "Fanout Optimization and Register Replication".
  * **AMD / Xilinx UG912**, *Vivado Design Suite Properties Reference Guide*, Property: "MAX_FANOUT".
