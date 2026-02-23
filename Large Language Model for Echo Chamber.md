## 探討具體方法：文字語意 --轉換成--> 數值

### Summary - 可重現性（Reproducibility）推斷

#### 已知範例
在 Section 5 中，提到：「 $T_g$ 作為生成新貼文的提示語句範本，根據個體 $i$ 的最近貼文與鄰居 $j$ 的最近貼文，來生成新貼文。上述 $T_g$ 的範例，藉由調整特定任務指示與輸入內容，可適用於 $T_o$ 和 $T_r$」。

#### 二、合理推斷
> 以下為根據上述論文 Section 3 + Section 5 內容的合理推斷（注意：論文並未明確說明）

- $T_o$ 的 LLM output：生成**新意見**（對應影響函數 $f$）
- $T_r$ 的 LLM output：生成**具體數值**（對應一致性函數 $g$，需為 $0 \leq g \leq 1$ 的數值以代入機率公式）

#### 三、可重現性缺口
Paper 在 Section 5 提供 $T_g$ 的 Prompt 具體範例，但 $T_o$ 與 $T_r$ 的具體 Prompt 格式，並未在 Paper 中詳細說明。

---

## Section 3.1 意見演化算法

$$O_i(t+1) = O_i(t) + \frac{1}{|N_i(t)|} \sum_{j \in N_i(t)} f(O_j, O_i(t))$$

### 說明
- $O_i(t)$：個體 $i$ 在時間 $t$ 所持有的意見
- $N_i(t)$：在有向圖中，指向個體 $i$ 的鄰居集合
- $f(O_j, O_i(t))$：影響函數，表示個體 $i$ 的鄰居 $j$ 對個體 $i$ 意見的影響

---

## Section 3.2 網路重組機率算法

### 說明
根據**意見一致性**與**推薦機制**，利用機率動態調整個體間的連接，反映現實社群的 Follow / Unfollow 行為：

$$P_{\text{unfollow}}(i, j) \propto 1 - g(O_i, O_j)$$

$$P_{\text{follow}}(i, k) \propto g(O_i, O_k)$$

- $g(O_i, O_j)$：一致性函數，表示個體 $i$ 和個體 $j$ 的意見相似性，範圍為 $0 \leq g \leq 1$

---

## Section 3.3 LLM 增強方法

在 LLM 增強框架下，影響函數 & 一致性函數定義調整為：

$$f(O_j, O_i(t)) = \text{LLM}(T_o(C_i, C_j))$$

$$g(O_i, O_j) = \text{LLM}(T_r(C_i, C_j))$$

$$y_i(t) \sim \text{LLM}(T_g(C_i, \{C_j \mid j \in N_i(t)\}))$$

### 說明
- $C_i$：個體 $i$ 的歷史內容，即個體 $i$ 最近貼文的子集
- $T$ 提示語句範本（Prompt Template）
  - $T_o$：動態意見（Opinion Dynamic）
  - $T_r$：重組一致性（Rewiring Compatibility）
  - $T_g$：生成內容（Content Generation）
- LLM 根據個體 $i$ 最近貼文 + 鄰居 $j \in N_i(t)$ 的最近貼文，輸出個體 $i$ 的新貼文

透過這三種範本結構，確保 LLM 能動態應對文章內容，將**動態意見**、**決策重組**、**文章生成**融合成互相作用且邏輯一致的框架（Cohesive Framework）。

---

## Section 5 提示語句範本

提示語句引導 LLM 有效地為使用者行為建模，並維持任務間的一致性。

### 一、設計原則
1. **標準化**：明確定義任務與輸出格式，確保可重現性
2. **邏輯推理（Chain-of-Thought）**：一步步推理，提升輸出結果的可解釋性與可靠性

### 二、具體範例
$T_g$ 作為生成新貼文的提示語句範本，根據個體 $i$ 的最近貼文與鄰居 $j$ 的最近貼文，來生成新貼文。
上述 $T_g$ 的範例，藉由調整特定任務指示與輸入內容，可適用於 $T_o$ 和 $T_r$。
