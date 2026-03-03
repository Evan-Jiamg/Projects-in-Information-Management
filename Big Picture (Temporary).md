# Big Picture：LLM 意見動態模擬 × Game Theory 驗證框架

---

## 壹、總結

- 核心想法：

將 Coevolutionary Opinion Formation Games 中，原本基於**一維數值差**的 K-NN 選鄰法，替換成根據 **SBERT 語意向量的 Euclidean Distance**，在更高維度的語意向量空間中，實作 K-NN，並驗證 Game Theory 的 PoA 數學界限，在此設定下是否成立。

---
## 貳、假設（待驗證）

| 假設 | 說明 |
|---|---|
| SBERT 向量能代表 Intrinsic Opinion | 以初始貼文向量作為 $s_i^0$ 是否真能代表 Agent 真實立場，初始貼文裡表達，搞不好不是真正的內在想法 |
| Equilibrium 閾值 $\delta$ 的選定 | $\delta$ 設定會影響 Equilibrium 判斷，若 $\delta$ 變化時，PoA 幾乎不變，結果會更可信 |

---

## 參、整合架構

### 一、文字轉換層

- 每位 Agent 在每個 Time Step 會產生一篇文字貼文，這篇貼文需要被轉換成兩種不同的表示形式，分別用於不同目的

```
Agent 的 Textual Opinion（貼文 y(t)）
        ├──→ Opinion Classifier f_oc (FLAN-T5-XXL)
        │         → Numeric Rating o ∈ {-2,-1,0,1,2}
        │         → 用於【意見更新層】
        │
        └──→ SBERT
                  → 固定維度語意向量
                  → 用於【K-NN 選鄰層】
```

### 二、K-NN 選鄰層

- 在 Coevolutionary Opinion Formation Games 的非對稱模型中，每位 Agent i 的鄰居集合定義為：

$$N(i) = \{ K \text{ agents with smallest } |z_j - s_i| \}$$

- 將 Coevolutionary 框架的核心替換，把一維數值的距離，改成向量空間 Euclidean Distance

  (同樣滿足 Coevolutionary 模型對 Convex + Symmetric 要求)：

$$N(i) = \{ K \text{ agents with smallest } \|\text{SBERT}(z_j^t) - \text{SBERT}(s_i^0)\|_2 \}$$

$$\text{Euclidean Distance: } \|a - b\|_2 = \sqrt{\sum_k (a_k - b_k)^2}$$

**對應關係：**

| Coevolutionary 原本 | 新框架 |
|---|---|
| $s_i$（Intrinsic Opinion） | Agent $i$ **初始貼文**的 SBERT 向量（固定不變） |
| $z_j$（Expressed Opinion） | Agent $j$ 在時間 $t$ 的**當前貼文**的 SBERT 向量（隨模擬更新） |
| $\|z_j - s_i\|$（數值差） | $\|\text{SBERT}(z_j^t) - \text{SBERT}(s_i^0)\|_2$（Euclidean Distance） |

---

### 三、意見更新層

鄰居選定之後，意見更新沿用 Echo Chamber 的意見演化算法：

$$O_i(t+1) = O_i(t) + \frac{1}{|N_i(t)|} \sum_{j \in N_i(t)} f(O_j, O_i(t))$$

其中影響函數由 LLM 驅動：

$$f(O_j, O_i(t)) = \text{LLM}(T_o(C_i, C_j))$$

- $C_i$：Agent $i$ 的歷史貼文內容
- $T_o$：Opinion Dynamic Prompt Template
- $N_i(t)$：由上方 SBERT K-NN 選出的鄰居集合

---

### 四、PoA 驗證層

#### Step 1：定義 Social Cost

- 模擬完成後，目標是驗證結果是否符合 Coevolutionary 論文給出的 PoA 理論界限

- 沿用 Coevolutionary 非對稱模型，將數值差替換為 Euclidean Distance：

$$C_i = \sum_{j \in N(i)} d(z_i^t, z_j^t)^2 + \rho K \cdot d(z_i^t, s_i^0)^2$$

其中：

$$d(a, b) = \|\text{SBERT}(a) - \text{SBERT}(b)\|_2$$

- 第一項：Agent $i$ 與鄰居的 Expressed Opinion 差異成本
- 第二項：Agent $i$ 的 Expressed vs. Intrinsic Opinion 差異成本
- $\rho$：自身意見的權重（$\rho$ 大 → Narrow-minded；$\rho$ 小 → Broad-minded）

#### Step 2：定義 Equilibrium

當所有 Agent 的意見 Opinion Classifier 的數值，在連續數個 Time Steps 內，不再顯著變化時，作為達到 Equilibrium。

當所有 Agent 的輸出數值，在連續 $\Delta t$ 個 Time Steps 內的變化量小於閾值 $\delta$，視為達到 Equilibrium：

$$\max_i |o_i^{t+\Delta t} - o_i^t| < \delta$$

#### Step 3：定義 Optimal Outcome

跑多組不同初始條件 (比如：不同初始 Persona / 貼文內容) 模擬，取 Total Social Cost 最低的結果作為 $C(\text{opt})$：

$$C(\text{opt}) = \min_{\text{runs}} \sum_i C_i$$

#### Step 4：計算 PoA 並對比理論界限

$$\text{PoA} = \frac{C(z^*)}{C(\text{opt})}$$

對比 Coevolutionary 理論預測的上下界：

$$\frac{1}{\rho^2} \leq \text{PoA} \leq \frac{(7+\varepsilon)(2+\varepsilon)}{1+\varepsilon}, \quad \rho = 1 + \varepsilon,\ \varepsilon > 0$$
