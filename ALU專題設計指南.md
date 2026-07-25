# 32-bit ALU Vivado 專題設計指南
### 給推甄數位IC設計組用的「優化前 / 優化後（Pipeline）」對照專題

> 備註：你提供的 Notion 連結因為網址過長且可能需要登入權限，我這邊無法直接讀取內容，所以以下建議是根據你的需求描述與 learning_log.md 撰寫。如果 Notion 裡有已經定案的架構或時程，可以貼文字內容給我，我再幫你對齊調整。

---

## 一、32-bit ALU 需要哪些元件？各自的作用與原理

一顆完整、夠「有份量」的 32-bit ALU，建議至少涵蓋以下六大類元件。這也是口試時教授最常會問「你的 ALU 支援哪些運算、怎麼實現」的標準答案結構。

### 1. 運算元輸入與 Opcode 解碼器
- **元件**：`a[31:0]`, `b[31:0]`（運算元）、`alu_op[3:0]` 或 `[4:0]`（控制碼）
- **作用**：opcode 決定這一拍要執行哪一種運算（ADD/SUB/AND/OR/XOR/NOT/SLT/SLL/SRL/SRA…）
- **原理**：本質上是一個 **decoder**，把二進位控制碼展開成多條 one-hot 選擇信號，驅動後面的功能單元與輸出 MUX。這部分建議做成表格化設計（opcode table），口試時可以直接秀出來，展現你有做「指令集/操作集」層級的規劃。

### 2. 加法/減法器（Adder/Subtractor）— ALU 的核心，也是你優化的主戰場
- **原理**：減法用二補數實現，`A - B = A + (~B) + 1`。做法是把 B 先與 `sub` 訊號做 XOR（`sub=1` 時等於逐位元取反），carry-in 直接接 `sub`，這樣同一個加法器硬體就能同時做加法與減法，是業界標準手法。
- 這裡就是你 baseline 版本用 **Ripple Carry Adder (RCA)**、優化版本要換架構或加 pipeline 的地方，詳見第二、三節。

### 3. 邏輯運算單元
- **元件**：AND / OR / XOR / NOT（NOR 也常見）
- **原理**：純組合邏輯，32 個 bit 平行運算，沒有進位鏈，所以延遲固定且很短，不是效能瓶頸。

### 4. 移位器（Shifter）
- **元件**：邏輯左移 SLL、邏輯右移 SRL、算術右移 SRA
- **原理**：不要用「一次移一位、移 n 次」的序列式做法（那是給移位暫存器用的），標準做法是 **Barrel Shifter**：用 log2(32)=5 級的 2-to-1 mux 樹，每一級決定要不要移動 2^k 位元，總延遲是 O(log n) 而不是 O(n)。這是你可以在報告中特別強調「用了 barrel shifter 而不是逐位移」的加分點。SRA 的部分要注意用 sign bit 補位（對應你日誌裡 signed extension 的概念）。

### 5. 比較器 / SLT（Set Less Than）
- **原理**：不用另外做一個減法器，直接借用第 2 點的加減法結果：`A - B` 的結果，若 `sign XOR overflow = 1` 表示 `A < B`（處理 signed 溢位的正確判斷法），這樣可以省一份硬體，也是常見的教科書技巧（可省面積、共用資源）。

### 6. Flag / 狀態旗標產生電路
- **Zero flag**：對 32-bit 結果做 **NOR reduction**（全 0 才輸出 1）
- **Carry-out**：加法器最高位的進位輸出
- **Overflow**：`overflow = Cin(MSB) XOR Cout(MSB)`，即最高位的進位輸入與進位輸出不同時代表溢位，這是比「比較兩數符號位」更通用、也更常在教科書/文獻中被引用的判斷式
- **Negative/Sign**：結果的 MSB

### 7. 輸出 MUX
- **原理**：把上面各功能單元的結果，依 opcode 選出最終輸出。這裡也是你之後做 pipeline 時要特別注意「各運算延遲要對齊」的地方（見第三節）。

---

## 二、優化前版本（Baseline）：Single-Cycle 純組合邏輯 ALU

**架構重點**：
- 所有功能單元都是**純組合邏輯**（`assign` / `always@(*)`），一個 clock cycle 內從輸入直接算到輸出（甚至可以完全不用 clock，只用輸出入緩衝暫存器方便 testbench 觀察）。
- 加法器使用 **Ripple Carry Adder（RCA）**：32 個 full adder 串接，carry 要一路傳到最高位才能得到正確結果。

**原理與瓶頸（這段是你報告中「為什麼需要優化」的立論基礎）**：
- RCA 的關鍵路徑（critical path）長度與位元數成正比，32-bit RCA 的最壞情況延遲約為 `3 + 31×2` 個 gate delay（第一級進位形成 3 級邏輯，之後每級 carry 傳遞約 2 級邏輯），這是被廣泛引用的分析結果<cite index="10-1">，因為每個全加器都要等前一級的進位運算完成才能繼續，所以進位延伸的加法器在位元數增加時會相對變慢</cite>。
- 也就是說：加法器的正確性完全沒問題，但因為進位要「一路傳到底」，最壞情況（例如 `0xFFFFFFFF + 1`）的路徑非常長，直接限制了整個電路能跑的最高頻率（Fmax）。

**這版本要交付的東西**：
1. RTL（純組合邏輯 ALU + RCA）
2. Testbench（含 corner case：全 0、全 1、進位鏈最長情況、signed overflow）
3. Vivado 綜合/實作後的 **Timing Report**（記下這版本的 WNS、可達到的 Fmax），這組數字是等等要拿來對比的基準線。

---

## 三、優化後版本：架構優化（快加法器）＋ Pipeline

你提到主要想切 pipeline，這邊建議做「**雙重優化**」，這也是數位 IC 相關文獻中很常見、且教授一眼就能看懂你有理解優化本質的做法：

### 優化 A：先換掉 RCA，改用 Carry Select Adder（CSA）
在切 pipeline 之前，先把最核心的瓶頸元件換掉，理由是：**pipeline 只能把「同一段延遲」切開分攤到多拍，不能讓單一組合邏輯本身變快**；如果 baseline 用的還是 RCA，pipeline 效果會被進位鏈拖累。所以更完整的優化故事是「先優化電路結構，再優化時序切分」。

- **Carry Select Adder 原理**：把 32-bit 加法器切成幾個區塊（例如 4 個 8-bit block），每個高位區塊**同時**用 `cin=0` 和 `cin=1` 各算一次（兩份 RCA 平行跑），等前一區塊真正的 carry-out 出來後，用一個 MUX 直接選結果，不用再重新算一次加法。這樣把「等待進位」的動作，從「等加法算完」降級成「等一個 MUX select」，速度明顯提升。<cite index="1-1">Carry select adder 在速度上被認為是最好的選擇之一，介於 ripple carry adder 與 carry look-ahead adder 之間取得平衡，只是會多付出一些面積代價</cite>。
- 這個技巧在文獻中有明確的量化效果：<cite index="2-1">有研究透過修改 CSLA 架構、改用其他加法器區塊取代傳統 RCA 區塊，讓延遲改善了約 44.7%，面積增加約 15.1%</cite>，這種「時間換面積」的 trade-off 分析，正是你報告中可以放的量化論述。

### 優化 B：插入 Pipeline 暫存器，切分關鍵路徑
在 CSA 架構下，天生就很適合切 pipeline，因為 CSA 本來就是「先平行算兩種可能，最後再選」的結構，切點很自然：

- **Stage 0（輸入暫存）**：鎖存 `a, b, alu_op`
- **Stage 1（部分運算）**：
  - 低 16 bit 直接算出真實 sum、產生 carry
  - 高 16 bit 同時算出「假設 cin=0」與「假設 cin=1」兩組 sum（這步驟本來就會平行跑，現在把這個中繼結果鎖進暫存器）
  - 邏輯運算（AND/OR/XOR）、移位器結果也在這一級算好、鎖存
- **Stage 2（選擇與輸出）**：用低 16 bit 真正的 carry-out 去選高 16 bit 的正確結果，組合輸出 MUX、計算 flag（zero/overflow/negative），最後鎖進輸出暫存器

**Pipeline 的原理（一定要在報告中講清楚，這是教授會考的重點）**：
- Pipeline 不會讓「一筆資料算完的時間（latency）」變短，甚至會因為多了暫存器而略增；它改善的是 **throughput（吞吐量）**：把長的組合邏輯路徑切成數段較短的路徑，每段路徑的延遲變短，因此可以用更高的 clock frequency 運作，並且每個 clock cycle 都能開始處理一筆新的運算（滿水位後一拍出一筆結果）。
- 這是經典的「面積/暫存器數量」換「頻率/吞吐量」的設計手法，在 CPU pipeline 設計、DSP 高速運算單元中非常標準。你可以在報告裡強調：因為你的 ALU 是「無資料相依（stateless）的組合功能單元」，插入 pipeline 完全不會有 data hazard 的問題（跟五級 MIPS pipeline 裡的 ALU 不同，那邊的 hazard 來自「指令之間」的相依，不是 ALU 電路本身）——這個區分能展現你對 pipeline 概念理解得很扎實，不是死記名詞。
- 有實測數據可以引用：<cite index="11-1">有研究指出，將運算單元做 pipeline 化實作後，操作頻率大約可以提升 4%</cite>，雖然數字看似不大，但重點是這類優化在真實處理器/加速器設計中是持續疊加的標準手法之一。
- 更積極的案例：<cite index="15-1">曾有設計把 32-bit 浮點加減單元做成 11 級 pipeline，達到每個 clock cycle 都能輸出一筆結果的吞吐量，並在 Xilinx FPGA 上跑到接近 900MHz</cite>，這說明「切多深的 pipeline」與「能跑多快」是高度相關、且是業界持續在做的優化方向，你的 3 級 pipeline 可以類比成簡化版的同一種思路。

### 效能對比一定要做「同延遲對齊」
記得邏輯運算、移位器這些原本延遲較短的路徑，也要配合加法器的 pipeline 深度插入對應的暫存器級數（即使邏輯上不需要），確保 MUX 選出來的各分支資料是「同一拍」的結果，這叫 **balanced pipeline**，是很多初學者設計 pipeline 時漏掉、導致功能錯誤的地方，寫在報告裡也是加分點。

---

## 四、怎麼在 Vivado 裡做出「有數據」的優化證明（最重要的加分項目）

教授很吃「有沒有量化證據」，不要只寫「我覺得比較快」。具體做法：

1. **兩版本都寫 XDC 約束檔**，用 `create_clock` 給一個初始週期（例如寬鬆的 20ns），先跑過 Synthesis + Implementation。
2. 打開 **Report Timing Summary**，記錄兩版本的 **WNS（Worst Negative Slack）**，用 `理論最高頻率 Fmax ≈ 1 / (約束週期 − WNS)` 反推兩版能跑到的最高頻率，做成表格比較。
3. 打開 **Report Utilization**，記錄兩版本用的 LUT / FF 數量，說明優化後版本因為多了 pipeline register 與 CSA 的平行硬體，面積會增加多少 %，藉此完整呈現 **speed vs. area 的 trade-off**，而不是只講好處不講代價（這種誠實的工程權衡分析，通常比只吹噓速度更能說服教授）。
4. Testbench 部分，兩版本都要用同一組 golden model（可以用 Verilog 內建運算子 `+ - & | ^` 等直接算出預期值，寫成 self-checking testbench，對應你日誌裡已經學過的 `if-else` + `$display`/`$fatal` 技巧），確保優化後的版本「功能等價」，這是任何優化都必須先證明的前提。
5. 如果想再加分：畫一張「輸入到輸出訊號經過幾級暫存器」的時序圖（timing diagram），標出 pipeline latency（例如 3 cycle），搭配 waveform 截圖，讓教授一眼看懂你確實理解自己切的 pipeline 在做什麼。

---

## 五、你可以在書面/口試中引用的文獻方向

（建議直接去 Google Scholar / IEEE Xplore 用這些關鍵字找到完整全文，附在報告的參考文獻中）

- Ripple Carry Adder 延遲分析與各類加法器（RCA/CLA/CSA/CSLA）比較：**"Comparison among Different Adders", IOSR Journal of VLSI**；**"Design and Comparative Analysis of Various Adders", IJCSIT**
- Carry Select Adder 架構改進與量化延遲改善（44.7% 延遲改善案例）：**IEEE "An analysis technique for improving delay factor of carry select adder using FPGA"**
- Adder 面積/延遲/功耗總覽比較：**"Area, Delay and Power Comparison of Adder Topologies"**
- Pipeline 化運算單元對頻率的實測影響：**"Design of 32-bit 3-Stage Pipelined Processor based on MIPS in Verilog HDL and Implementation on FPGA Virtex7"**
- 教科書（口試被問原理時的權威後盾）：
  - Neil H. E. Weste & David Harris, *CMOS VLSI Design: A Circuits and Systems Perspective*（加法器架構、pipeline/retiming 章節）
  - Jan M. Rabaey, *Digital Integrated Circuits: A Design Perspective*
  - David & Sarah Harris, *Digital Design and Computer Architecture*（ALU 與 pipeline 概念，MIPS 範例）

---

## 六、根據你的學習日誌，評估目前的準備程度與缺口

先講結論：你的 **Verilog 語法基礎、時序/組合邏輯觀念、CDC/亞穩態、低功耗設計概念**這幾塊已經打得很扎實（尤其 7/24 那篇對 CDC、Async FIFO、Power Gating 的整理，深度已經超過一般大三生），但這些多半還停留在「觀念/筆記」階段，離「做出一個能被量化評估、有 pipeline 的 ALU 專題」還有幾個具體缺口：

### 缺口 1：Vivado 實作流程只到「模擬」，還沒碰「時序分析（STA）」
目前日誌只到 half adder 的功能模擬（7/23），還沒有 Synthesis / Implementation / Timing Report / XDC 約束檔的實作經驗。**這是這個專題最關鍵的一塊**，因為你整個「優化前後比較」的說服力都建立在 Fmax、WNS 這些時序數據上。
→ **建議學習**：Vivado 的 `create_clock`、Timing Summary 報表怎麼看（WNS/TNS/Slack）、Utilization 報表，可以先拿你已經寫好的 half adder 或 Adder1/Adder2 練習跑一次完整 synthesis→implementation 流程。

### 缺口 2：加法器架構理論還停留在「功能正確」，沒學到「速度優化」層級
日誌裡的 Adder1/Adder2 練習重點都在「怎麼把模組正確接起來」（by position/by name、進位訊號處理），這很重要但屬於**結構層面**；還沒有出現 Carry Lookahead、Carry Select 這類**針對延遲做優化**的加法器理論。
→ **建議學習**：Carry Lookahead Adder 的 Generate/Propagate 概念、Carry Select Adder 的雙路徑平行運算概念，這兩者是這個專題「優化」的核心，也是面試很愛問的經典題。

### 缺口 3：還沒有實際切過 Pipeline 的經驗
日誌中對 clock/reset/FF 的觀念都對，但目前所有練習都是**單一組合邏輯或簡單時序電路**（分頻器、移位暫存器、計數器），還沒有「把一段長組合邏輯切成多級、插暫存器」的實作經驗，也還沒碰過 **balanced pipeline（分支延遲對齊）**這個常見陷阱。
→ **建議學習**：找一個簡單的多級管線範例（例如乘法器 pipeline 或這次的 ALU）親手切一次，體會「latency 增加、throughput 提升」的實際差異，而不是只背名詞。

### 缺口 4：Testbench 還在 `$display`/`$monitor` 階段，尚未做隨機化/自動化驗證
你已經會寫 self-checking（`if-else` + `$fatal`），這對這個專題已經**夠用**，但如果想在推甄時展現更進階的驗證能力（IC設計組面試常考驗證觀念），可以再往上一層。
→ **可選加分項**：學習簡單的隨機測試向量產生（`$random`）、或者了解 Assertion-Based Verification（SystemVerilog Assertions, SVA）的基本概念，即使專題不用，口試被問到也能對答如流。

### 缺口 5：尚無「量化實驗結果」的呈現經驗
目前日誌是學習筆記性質，還沒有「做完一個東西、量測數據、畫圖比較」的完整實驗流程紀錄。
→ **建議**：這個 ALU 專題做完後，把 WNS/Fmax/LUT/FF 數據整理成一張對照表或長條圖，這會是你推甄書面資料中最有說服力的一頁，比大段文字描述更有效。

### 你已經具備、且是加分項的部分
- 對 signed/unsigned、溢位、位移運算元的理解已經很細緻，直接可以用在 overflow flag 與 SRA 的設計上。
- CDC / 亞穩態 / Async FIFO / 低功耗設計的整理，顯示你對「電路上晶片後的真實問題」有涉獵，這在推甄口試被問到「除了功能還考慮什麼」時會是很強的加分材料，即使這次 ALU 專題用不到，也建議口試時主動提及你自學過這些主題。

---

## 建議優先順序（如果要在有限時間內準備）
1. 先把 baseline ALU（組合邏輯 + RCA）功能做對，並學會看 Vivado 的 Timing/Utilization 報表（缺口1）
2. 學 Carry Select Adder 原理並實作（缺口2）
3. 切 3-stage pipeline，注意 balanced pipeline（缺口3）
4. 把兩版本數據做成對照表/圖（缺口5）
5. 有餘力再補 SVA / 隨機測試（缺口4，屬於加分而非必要）
