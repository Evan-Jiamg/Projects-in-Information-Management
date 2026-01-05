# Coevolutionary Opinion Formation Games

## 研究動機 + 核心概念

### 一、研究主題

Paper提出一個由 **社群網路的意見 (Opinions in Social Networks)** 所形成的博弈論模型 (Game-Theoretic Models)，研究意見 (Opinions) 和 Friendships 如何共同演化。

### 二、核心研究問題

#### 1. 個體如何選擇意見
- 具有固定 Intrinsic Opinions 的個體 A，會根據其他個體 Opinion，以及自身對該 Opinion 的信心，來選擇自己的 Expressed Opinion

#### 2. Social Network 如何形成
- Social Network 的形成取決於個體的 Expressed Opinion

### 三、理論基礎
  - Models 中的 Nodes 透過 **< Maximize 與朋友之間的意見一致性 >** 來形成自身 Opinion
  - 意見一致性取決於每個朋友間意見差異加權的關係強度
  - 透過概括 FOCS 2011 最新研究 – 有界信心模型 (Bounded Confidence Type Models)，來定義這一過程的均衡的社會成本
  - Bindel 等人透過考慮無政府狀態的代價 (Price of Anarchy, PoA)，來分析達到均衡時的品質

---

## Paper 分析方式

### 一、社會成本 (Social Cost)

- 衡量所有個體在均衡狀態下的總成本
- 包含：個體之間的 Expressed Opinion 差異成本 + 個體自身的 Intrinsic vs. Expressed Opinion 差異成本

### 二、無政府狀態的代價 (Price of Anarchy)

#### 1. 定義
$$
\text{PoA} = \frac{\text{均衡社會成本}}{\text{最適社會成本 (最小社會成本)}}
$$

$$
= \frac{\text{Cost of the Worst Equilibrium Outcome}}{\text{Optimal Social Outcome}}
$$

#### 2. 前提假設
- 假設個體 Intrinsic Opinion 皆固定，並且當下的 Optimal Social Outcome 被明確定義

## 三種 Equilibrium Solution 的概念

Paper 利用 3 種 Solution concept 定義 Equilibrium，再藉由 Equilibrium 集合計算 PoA，並且每種 Concept 限制都比前者寬鬆，所以能夠比較 Worst Equilibrium Outcome 和 Optimal Equilibrium Outcome 的差距

### A. Pure NE

**定義：**
- (：個體的任何其他可能意見)
- 在固定其他個體意見下，個體策略為純意見 (非隨機)

**條件：**
- 個體任何其他可能的意見，都不會比當前的策略更好

##### B. Mixed NE

**定義：**
- 個體選擇意見的策略為機率分布 (非純意見)

**條件：**
- 個體之間的「策略獨立」，並且要求「實際會用到的策略」必須一樣好，代表社會成本：現在用的策略 ≤ 任何替代策略

##### C. Correlated Equilibrium (CE)

**定義：**
- 先抽樣，再私下建議玩家採用特定策略

**條件：**
- 允許個體間「策略相關」，使用條件期望，讓個體知道自己被建議採用的情況下

#### Pure NE vs. Mixed NE vs. Correlated Equilibrium 三者間關係

**總結：Pure NE ⊆ Mixed NE ⊆ CE**

- Pure NE 的 PoA → Upper Bound (最緊的上界)
- CE 的 PoA → Lower Bound (最寬鬆的下界)

**關係證明：**
- A. Pure NE ⊆ Mixed NE
- B. Mixed NE ⊆ Correlated Equilibrium

---

## 局部平滑性論證 (Local Smoothness Arguments)

### 核心目的

透過局部平滑性論證，來嚴格限制產生的 PoA，並作為以下依據：
1. 描述 Nodes 對自身 Intrinsic Opinions 的重視程度
2. 作為 Nodes 對和自身意見更一致朋友，給予更多權重依據的函數

### 核心不等式

#### 前提假設
- 社會成本函數為連續且可微分

#### 公式

**左式：**
- 個體如果改成社會最優策略的成本
- ：個體 i 的最優策略
- ：個體 i 往最優策略移動下，社會成本減少量

**右式：**
- ：最優策略的社會成本
- ：目前策略的社會成本
- λ, μ：權重常數

#### 意義
- 個體的社會成本，能被「最優成本」與「目前成本」限制住

---

## 對稱模型 vs. 非對稱模型

Paper 研究在 **對稱 vs. 非對稱模型** 之下，Opinion 和 Friendship 共同演化的博弈，並為兩種模型提供 PoA 的上下界限

---

## 對稱模型

### 社會成本分析

#### 公式

#### 模型假設

- Social Network 是 **Fixed + Undirected**
- 權重函數具有：convex, differentiable, symmetric 特性
- ：個體的 Intrinsic Opinion
- ：個體的 Expressed Opinion
- ：個體相鄰的所有節點 (Neighbors of)

**成本函數解釋：**
- 第一項：個體 vs. 鄰居 Expressed Opinion 差異的成本
- 第二項：個體 Intrinsic vs. Expressed Opinion 差異的成本

### PoA 上下界分析

#### 上界 (Robust Upper Bound)

- (集合：Neighbor Effect 函數產生的 Constraints)
- (集合：Intrinsic Cost 函數產生的 Constraints)
- For

#### 下界 (Tight Lower Bound)

##### First Stage
給定，

**定義：**
- 因為為函數所限制的平面，所定義的 Convex Region，因此，最小值落在邊界

##### Second Stage
when,

- 最小值發生在 f, g 的交界，對應的參數為和
- 同時，when 和，Satisfy

##### Third Stage

為了證明 Lower Bound = 為 Tight，Paper 建構了 Game Instance，來使 PoA 接近該數值。

**Consider 具有 Intrinsic Opinions 的三個個體：**
- 定義 Nash 均衡結果為
- Social 最佳結果為

因此，透過選擇適當的權重 w 和函數 f、g (讓 Neighbor force = Intrinsic force)，確保每個個體一階微分，恰好符合 Pure NE 成立條件

由於 PoA 是「均衡解」與最優解的比值，所以需要證明 z 是均衡解，而選擇證明 Pure NE 是因為它是最強的 Lower Bound
- (Pure NE ⊆ Mixed NE ⊆ CE)

##### Fourth Stage

同時，Game Instance 也滿足 f, g 函數的約束性，並且等式成立，因此，將所有個體的等式相加後，便能得到

**Tight Lower Bound:**

---

## 非對稱模型

### 社會成本分析

#### 公式

#### 模型假設

- Social Network 是 **Dynamic + Directed**
- 引入參數：作為自身 Opinion 的權重比例

**成本函數解釋：**
- 大 → Narrow minded (目光狹隘，堅持己見)，個體的 Social Cost 下降
- 小 → Broad minded (心胸開闊，容易受他人意見影響)

### PoA 上下界分析

#### 上界

- ：上界
- ：上界
- 為單調遞減 (ρ 越大，上界越小)

**Proof:**

##### First Stage: 假設 z = s 情況 (表達意見 = 內在意見，代表所有個體都誠實表達)

- ：為 Optimal Solution of 誠實策略
- ：誠實策略之下，OPT 是近似
- ：誠實策略之下，OPT 是近似，為最優解

##### Second Stage: 局部平滑性不等式

- ：
- ：

##### Third Stage: 利用 K-NN 性質

- 因為是 K 個最接近的
- 是 K 個最接近的
- 所以

##### Fourth Stage: 使用三角不等式

使用三角不等式：

##### Fifth Stage: 每個 j 最多出現在 2K 個集合中

因為在實數線上，for any
- 最多有 K 個 such that and
- 最多有 K 個 such that and
- 因此，總共最多 2K 個

##### Final Stage: 最終不等式

Let：

Thus,

Then, by combining factor from Lemma 4.2

**Lemma 4.2:**

**補充：**
- 因為 Pure NE 可能不存在 (Proposition 4.1: 當, , and weight, Pure NE 不存在)，所以採用 Local Smoothness，來分析 Correlated Equilibrium

#### 下界

**Proof:**

##### 概念
1. 先找到一個壞的 Nash Equilibrium (成本高)
2. 再找到一個好的解 (接近最優，成本低)
3. 證明

##### 詳細證明過程

(後續內容...)

---

## References

研究筆記內容基於論文 "Coevolutionary Opinion Formation Games" 的分析整理

## License

This work is for academic and educational purposes.

