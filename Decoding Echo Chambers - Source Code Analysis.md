# Decoding Echo Chambers - Source Code Analysis

> **Paper:** Decoding Echo Chambers: LLM-Powered Simulations Revealing Polarization in Social Networks (COLING 2025)  

---

## Contents

1. [Experimental Results — LLM Simulation](#1-experimental-results--llm-simulation)
2. [Comparison — LLM vs. Traditional Numerical Methods (自行實作)](#2-comparison--llm-vs-traditional-numerical-methods-自行實作)
3. [Visualization btw. LLM vs. Numeric](#3-visualization-btw-llm-vs-numeric)
4. [Review - Main Process](#4-review---main-process)
5. [Review - Recommendation Algorithm](#5-review---recommendation-algorithm)
6. [Review - Opinion Update Algorithm](#6-review---opinion-update-algorithm)
7. [Review - Network Construction](#7-review---network-construction)
8. [Detailed Implementation of Numeric Method](#8-detailed-implementation-of-numeric-method)
   
---

## 1. Experimental Results — LLM Simulation

> Belief Value 記錄格式範例：
> ```json
> {"step": 1,  "date": "2024-01-02 00:00:00", "polarization": 0.30, "neighbor_correlation_index": 0.76}
> ...
> {"step": 10, "date": "2024-01-11 00:00:00", "polarization": -0.28, "neighbor_correlation_index": 1.08}
> ...
> {"step": 30, "date": "2024-01-31 00:00:00", "polarization": -0.06, "neighbor_correlation_index": 1.48}
> ```

### Scale-Free Network

| Step | Polarization (Random) | Polarization (Similarity) | NCI (Random) | NCI (Similarity) |
|:---:|:---:|:---:|:---:|:---:|
| 1  | 0.30  | 0.20  | 0.76 | 0.84 |
| 10 | -0.28 | -0.22 | 1.08 | 1.32 |
| 20 | -0.44 | -0.26 | 1.44 | 1.48 |
| 30 | -0.46 | -0.40 | 1.68 | 1.80 |

### Small-World Network

| Step | Polarization (Random) | Polarization (Similarity) | NCI (Random) | NCI (Similarity) |
|:---:|:---:|:---:|:---:|:---:|
| 1  | 0.24  | 0.28  | 1.56 | 1.16 |
| 10 | -0.38 | -0.52 | 2.12 | 2.24 |
| 20 | -0.44 | -0.60 | 2.44 | 2.84 |
| 30 | **-0.56** | **-0.62** | **3.48** | **3.08** |

### Random Graph

| Step | Polarization (Random) | Polarization (Similarity) | NCI (Random) | NCI (Similarity) |
|:---:|:---:|:---:|:---:|:---:|
| 1  | 0.16  | 0.22 | 0.88 | 0.88 |
| 10 | 0.04  | 0.22 | 1.44 | 1.16 |
| 20 | -0.04 | 0.12 | 1.56 | 1.44 |
| 30 | -0.06 | 0.18 | 1.48 | 1.76 |

### 三種網路綜合比較（Step 30）

| Network | Method | Polarization | NCI |
|---|---|:---:|:---:|
| Random Graph | Random     | -0.06 | 1.48 |
| Random Graph | Similarity | 0.18  | 1.76 |
| Scale-Free   | Random     | -0.46 | 1.68 |
| Scale-Free   | Similarity | -0.40 | 1.80 |
| Small-World  | Random     | **-0.56** | **3.48** |
| Small-World  | Similarity | **-0.62** | **3.08** |

**Network 觀察：**
- **Random Graph** Polarization 不明顯  — 因為隨機連接，無中心節點，影響力分散
- **Small-World** Polarization 最明顯，NCI 也最高 — High Clustering 特性，讓群體內部意見快速趨同，Echo Chamber 最快形成
- **Similarity Method** 在所有 Network 下，NCI 偏高 — 篩選 Belief 差距 ≤ 2 的鄰居，強化同質互動

---

## 2. Comparison — LLM vs. Traditional Numerical Methods (自行實作)

- Abstract Guideline：在同張 Network 裡，以不同比例 α ，控制 Type-C（Numeric，cost-minimizing） 和 Type-L（LLM-driven） 兩種 Agent 數量 ，每輪互動後，再用 k-NN Rewiring 更新鄰居，最終量化成 Social Cost 和 PoA。

- My Progress：先做 Numeric (BCM、FJ、DeGroot) vs. LLM Model，分析在同張 Network 的表現。
  
### Experimental Result

#### BCM

| Network | Step | Polarization | NCI |
|---|:---:|:---:|:---:|
| Scale-Free  | 1  | 0.073 | -0.153 |
|             | 10 | 0.071 | -0.144 |
|             | 20 | 0.060 | -0.090 |
|             | 30 | 0.060 | -0.089 |
| Small-World | 1  | 0.092 | 0.378 |
|             | 10 | 0.115 | 0.316 |
|             | 20 | 0.116 | 0.302 |
|             | 30 | 0.116 | 0.300 |
| Random      | 1  | 0.109 | 0.032 |
|             | 10 | 0.062 | 0.184 |
|             | 20 | 0.054 | -0.011 |
|             | 30 | 0.054 | 0.010 |

#### FJ

| Network | Step | Polarization | NCI |
|---|:---:|:---:|:---:|
| Scale-Free  | 1  | 0.087 | 0.469 |
|             | 10 | 0.047 | 0.378 |
|             | 20 | 0.047 | 0.378 |
|             | 30 | 0.047 | 0.378 |
| Small-World | 1  | 0.075 | 0.709 |
|             | 10 | 0.043 | 0.648 |
|             | 20 | 0.043 | 0.648 |
|             | 30 | 0.043 | 0.648 |
| Random      | 1  | 0.101 | 0.455 |
|             | 10 | 0.077 | 0.334 |
|             | 20 | 0.077 | 0.334 |
|             | 30 | 0.077 | 0.334 |

#### DeGroot

| Network | Step | Polarization | NCI |
|---|:---:|:---:|:---:|
| Scale-Free  | 1  | 0.185 | 0.806 |
|             | 10 | 0.199 | 0.992 |
|             | 20 | 0.199 | 0.989 |
|             | 30 | 0.199 | 0.993 |
| Small-World | 1  | 0.128 | 0.948 |
|             | 10 | 0.130 | 0.997 |
|             | 20 | 0.131 | 0.997 |
|             | 30 | 0.131 | 0.998 |
| Random      | 1  | 0.132 | 0.783 |
|             | 10 | 0.150 | 0.979 |
|             | 20 | 0.149 | 0.990 |
|             | 30 | 0.150 | 0.997 |

### 差異對照（Step 30，Small-World）

| 方法 | 類型 | Polarization | NCI | 收斂行為 |
|---|---|:---:|:---:|---|
| LLM (Random)     | LLM-based | -0.56 | 3.48 | 持續變動，未收斂 |
| LLM (Similarity) | LLM-based | -0.62 | 3.08 | 持續變動，未收斂 |
| BCM              | Numerical | 0.116 | 0.300 | 快速收斂 |
| FJ               | Numerical | 0.043 | 0.648 | 快速收斂 |
| DeGroot          | Numerical | 0.131 | **0.998** | 快速收斂 |

**Observation：**
- **傳統模型**（BCM / FJ / DeGroot）因線性更新規則，Polarization 普遍偏低，大多在 Step 10 後，快速收斂，無法捕捉人類意見的波動性
- **DeGroot** NCI 趨近 1，代表該模型假設社群最終達到完全共識，與現實明顯不符

## 3. Visualization btw. LLM vs. Numeric

<img width="2352" height="786" alt="image" src="https://github.com/user-attachments/assets/db559168-d757-413e-81bc-2edc502217ff" />

<img width="2353" height="806" alt="image" src="https://github.com/user-attachments/assets/bf515775-3bdf-4507-b8f3-d132ed499c50" />

<img width="2344" height="776" alt="image" src="https://github.com/user-attachments/assets/5abbe325-fdf3-4362-9e59-aa5140a5ea06" />

---

## 4. Review - Main Process

每個 Simulation Step，所有 Agents 依序執行三個階段：

```
Step 1: 決定互動對象 (Recommendation Algorithm : Random / Similarity)  →  Step 2: 鄰居互動 + 交換意見  →  Step 3: 更新自身意見 (Opinion Update Algorithm)
```

- **Belief Value** 範圍：`-2`（strongly oppose）到 `+2`（strongly support）
- 每輪由 GPT-4o-mini (OpenAI API) 進行推理，更新 Agents 自身對該 Topic 的想法，輸出新的 Opinion + Belief Value

---

## 5. Review - Recommendation Algorithm

決定每個 Agent 這輪「要跟哪些 Neighbor 互動」。

### Random

互動對象：從全部鄰居中隨機打亂後，選 `max_interactions` 個。

```python
if recommendation == 'random':
    random.shuffle(neighbors)
    neighbors = neighbors[:self.max_interactions]
```

### Similarity

互動對象：選 Belief Value 差距 `≤ 2` 的鄰居，模擬 Echo Chamber 推薦（如F：acebook / Twitter 演算法）。

```python
elif recommendation == 'similarity':
    neighbors_selected = []
    for neighbor in neighbors:
        if abs(agent.belief - neighbor.belief) <= 2:
            neighbors_selected.append(neighbor)
    neighbors = neighbors_selected
```

### Dissimilarity（Paper 未採用，但保留在 Source Code 中，感覺未來能分析）

互動對象：選 Belief Value 差距 `≥ 2` 的鄰居，模擬 Opposite Opinion 互動。

```python
else:
    neighbors_selected = []
    for neighbor in neighbors:
        if abs(agent.belief - neighbor.belief) >= 2:
            neighbors_selected.append(neighbor)
    neighbors = neighbors_selected
```

---

## 6. Review - Opinion Update Algorithm

每輪互動後，Agent 透過 LLM 更新自身 Opinion + Belief Value。

### Step 1 — 生成短期記憶（Short-Term Memory）

- 收集本輪所有互動的鄰居意見，彙整成摘要：

```python
opinion_short_summary = get_summary_short(
    self.system_prompt,
    others_opinions,
    gpt_model=self.gpt_model,
    mitigation_perspectives=self.mitigation_perspectives,
    temp=self.temp
)
```

### Step 2 — 合併為長期記憶（Long-Term Memory）

- 將短期記憶 + 過去長期記憶送入 LLM，產生更新後的長期記憶。

```python
if self.with_long_memory:
    long_mem = get_summary_long(
        self.system_prompt,
        self.long_opinion_memory,
        opinion_short_summary,
        gpt_model=self.gpt_model,
        temp=self.temp
    )
    self.long_opinion_memory = long_mem
    self.long_memory_full.append(self.long_opinion_memory)
```

- Prompt 限制模型，只能 Input 兩段記憶 (Short-Term Memory + Previous Long-Term Memory)，且回覆開頭固定為 `"In my long-term memory, ..."`：

```
Recap of Previous Long-Term Memory: {long_memory}
Today's Short-Term Memory: {short_memory}

Task:
Using only the information in the previous long-term memory and today's
short-term memory, create an updated long-term memory.

Instructions:
- Do not introduce any new information that is not present in the provided memories.
- Start the updated memory with: "In my long-term memory, ..."
- Accurately combine key details from both memories into a clear summary.

Output:
- long_term_memory: Your new, consolidated long-term memory statement.
```

### Step 3 — 輸出新的 Opinion & Belief Value

- 將（舊 Opinion + 舊 Belief + 新長期記憶）送入 LLM，由 Model 決定是否改變立場
- 輸出具體的 `新 Opinion / 新 Belief / Reasoning`：

```python
def response_and_belief(self, user_msg, gpt_model):
    system_msg = self.system_prompt

    response = get_completion_from_messages_structured(
        system_messages=system_msg,
        messages=user_msg,
        model=gpt_model,
        temperature=self.temp,
        response_type=update_opinion_response
    )

    tweet     = response.opinion
    belief    = response.belief
    reasoning = response.reasoning

    return tweet, belief, reasoning
```

```
Your previous opinion: {opinion}
Your previous belief value: {belief}
Your long-term memory: {long_mem}

Belief values:
  -2: strongly oppose  |  -1: somewhat oppose  |  0: neutral
  +1: somewhat support |  +2: strongly support

Task:
Reflect on your opinion and belief, considering whether to maintain your
stance or adjust it based on your long-term memory.

Instructions:
- Think like a human: decide whether to hold firm or adapt based on the
  influence of the opinions you have heard.

Output:
  opinion   : 新意見，必須包含 belief_keywords 中的關鍵字，開頭為 "I {keyword}"
  belief    : 新 Belief Value（-2 ~ +2）
  reasoning : 是否受影響及理由
```

---

## 7. Review - Network Construction

使用 `networkx` 建構三種對應真實社群結構的網路。

### 7-1. Small-World Network

- 採用 WS Algorithm，具備：High Clustering + Short Avg. Path。

- `n`為節點數（Agent 數量），`k` 為每個節點初始連接最近 k 個鄰居（必須為偶數），再以`p` 為每條邊被隨機重連機率

```python
# 1st Part
def generate_small_world_network(num_agents, k, p):
    G = nx.watts_strogatz_graph(n=num_agents, k=k, p=p)
    return G

# 2nd Part
if network_type == "small_world":
    k = kwargs.get('k', 4)
    p = kwargs.get('p', 0.1)
    return generate_small_world_network(num_agents, k, p)
```

### 7-2. Scale-Free Network

- 採用 BA Algorithm，Paper 引入自訂 `adjust_node_degree()` 控制 Leader Node 的 Connection 數量。
- 標準 BA Algorithm，Leader 影響力是隨機決定，每次執行結果不同
- 為確保可控，Paper 將 Connection 最多的 2 個 Agents 調整為準確的 Connection `leader_edges=15`，並將其餘非 Leader 的 Agents，Connections 上限壓制在 `other_edges_limits=7`，避免產生與 Leader 相近影響力的 Agents

```python
# 1st Part
def generate_scale_free_network_new(num_agents, m, leader_edges, other_edges_limits):
    G = nx.barabasi_albert_graph(n=num_agents, m=m)
    sorted_nodes = sorted(G.degree, key=lambda x: x[1], reverse=True)

    leader1, leader2 = sorted_nodes[0][0], sorted_nodes[1][0]

    def adjust_node_degree(node, leader_edges):
        current_degree = G.degree(node)

        if current_degree < leader_edges:
            for other_node in range(num_agents):
                if not G.has_edge(node, other_node) and node != other_node:
                    G.add_edge(node, other_node)
                    if G.degree(node) >= leader_edges:
                        break

        elif current_degree > leader_edges:
            neighbors = list(G.neighbors(node))
            while G.degree(node) > leader_edges:
                G.remove_edge(node, neighbors.pop())

    adjust_node_degree(leader1, leader_edges)
    adjust_node_degree(leader2, leader_edges)

    for node, degree in sorted_nodes[2:]:
        if degree > other_edges_limits:
            adjust_node_degree(node, other_edges_limits)

    return G

# 2nd Part
elif network_type == "scale_free":
    m = kwargs.get('m', 3)
    return generate_scale_free_network_new(num_agents, m, leader_edges=15, other_edges_limits=7)
```

對應：Twitter / YouTube KOL 網路（少數意見領袖，主導流量）。

### 7-3. Random Graph

- 採用 ER Algorithm，無中心節點，每條邊以機率 `p` 隨機建立
  
```python
# 1st Part
def generate_random_network(num_agents, p):
    G = nx.erdos_renyi_graph(n=num_agents, p=p)
    return G

# 2nd Part
elif network_type == "random":
    p = kwargs.get('p', 0.1)
    return generate_random_network(num_agents, p)
```

對應：隨機互動場景。

---

## 8. Detailed Implementation of Numeric Method

### NumericAgent — Initialization + Standardization

- LLM 的 Belief Value 範圍為 `[-2, 2]`（`model.py:83` 將 Opinion Value `[-1, 1]`× 2 放大，使 GPT 能明確感知數值強度）。
- Numeric Method 直接沿用原始 Opinion Value，範圍 `[-1, 1]`，Initial Belief Value 從 JSON 載入，只有五個固定值：`-1.0, -0.5, 0.0, 0.5, 1.0`。
- `stubbornness` 沿用原先 Scale-Free Network 中調整 Agent 對自身 Opinion 堅持程度的數值，此處作為 FJ 模型的加權參數。
- `epsilon` 為 BCM 的 Belief Value 相差允許的最大值，預設 `0.5`。

```python
class NumericAgent(mesa.Agent):
    def __init__(self, unique_id, model, initial_belief, stubbornness=0.5, update_method='degroot', epsilon=0.5):
        super().__init__(model)
        self.unique_id = unique_id
        self.initial_belief = initial_belief
        self.belief = initial_belief
        self.stubbornness = stubbornness
        self.update_method = update_method
        self.epsilon = epsilon
        self.beliefs = [self.belief]
        self.opinions = [str(round(self.belief, 4))]
```

- 取得鄰居 Belief 數值：

```python
def get_neighbor_beliefs(self):
    neighbors = self.model.grid.get_neighbors(self.pos, include_center=False)
    return [n.belief for n in neighbors]
```

- 根據 `update_method` 執行對應的更新函式，並記錄每個 Step 的 Belief 數值與 Opinion：

```python
def step(self):
    if self.update_method == 'degroot':
        self.step_degroot()
    elif self.update_method == 'fj':
        self.step_fj()
    elif self.update_method == 'bcm':
        self.step_bcm()

    self.beliefs.append(self.belief)
    self.opinions.append(str(round(self.belief, 4)))
```

- LLM 與 Numeric Method 皆設定 `seed=50`，控制 `random` 和 `numpy` 的隨機行為，確保實驗結果可重現：

```python
def set_seed(seed):
    random.seed(seed)
    np.random.seed(seed)
    print(f"Seed set to {seed}")
```

### DeGroot Method

- 將 Agent 自身 + 所有 Neighbor 的 Belief Value，加總再取平均。

```
new_belief = (b1 + b2 + ... + bn) / n
```

```python
def step_degroot(self):
    neighbor_beliefs = self.get_neighbor_beliefs()
    if not neighbor_beliefs:
        return
    all_beliefs = neighbor_beliefs + [self.belief]
    self.belief = float(np.mean(all_beliefs))
```

### FJ（Friedkin-Johnsen）Method

- 將 Agent 的「初始 Belief 數值」 和「當前 Neighbor Belief 數值加總取平均」，透過 Stubbornness 來調整權重，得到新的 Belief Value，越高越固執，Agent 越難被鄰居說服

```
new_belief = (1 - s) × 鄰居平均 + s × 初始 belief
```

```python
def step_fj(self):
    neighbor_beliefs = self.get_neighbor_beliefs()
    if not neighbor_beliefs:
        return
    neighbor_avg = float(np.mean(neighbor_beliefs))
    self.belief = (1 - self.stubbornness) * neighbor_avg + self.stubbornness * self.initial_belief
```

### BCM（Bounded Confidence Model）Method

- 將當前 Agent 自身 Belief 數值 + Neighbor 的 Belief 數值（只選與自身差距 ≤ Epsilon 的 Neighbor），相加再取平均更新 Belief Value

```
eligible   = { b_i : |self.belief - b_i| <= epsilon }
new_belief = mean(eligible)
```

```python
def step_bcm(self):
    neighbor_beliefs = self.get_neighbor_beliefs()
    eligible = [b for b in neighbor_beliefs if abs(self.belief - b) <= self.epsilon]
    if not eligible:
        return
    self.belief = float(np.mean(eligible))
```
