# Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks
---

## 壹、句子嵌入 (Sentence Embedding)

- 定義：是將文本轉換為**固定維度數值向量**的方法。

- 核心目標：**語意相似的句子，在向量空間中也很靠近**，方便後續的語意運算。
- 
---

## 貳、Pooling 策略：從 BERT 輸出產生句子向量

- SBERT 在 BERT / RoBERTa 的輸出層後，增加一個 **Pooling Operation**，將輸出轉為固定大小的句子嵌入。

- 共有 3 種策略：

| 策略 | 說明 |
|---|---|
| **CLS-token** | 使用 CLS token 的輸出向量 |
| **MEAN**（預設） | 計算所有輸出向量的平均值 |
| **MAX** | 計算輸出向量的最大值 (Max-Over-Time) |

- 根據 Ablation Study，使用 Regression 目標函數時，MEAN 策略表現顯著優於 MAX。

---

## 參、網路架構 × 目標函數

網路架構與目標函數緊密連結，**訓練資料的形式決定兩者的選擇**。

### 一、Siamese 架構 + Classification 目標函數

- 適用資料：有標籤的分類資料（比如 NLI: entailment / contradiction / neutral）

- 將語意嵌入 $u、v 與對應元素差異 |u-v| 串接，再乘以可訓練權重 W_t \in \mathbb{R}^{3n \times k}$，優化 **Cross-Entropy Loss**：

$$o = \text{softmax}(W_t(u, v, |u-v|))$$

$$n：句子嵌入的維度$$

$$k：標籤的數量$$

- 流程圖: 
```
Sentence A → BERT → Pooling → u ─┐
                                  ├→ (u, v, |u-v|) → Softmax Classifier
Sentence B → BERT → Pooling → v ─┘
```

---

### 二、Siamese 架構 + Regression 目標函數

- 適用資料：有連續相似度分數的資料（比如：STS，分數範圍 0～5）

- 直接計算兩個句子嵌入 $u、v$ 的**餘弦相似度 (Cosine Similarity)**，優化 **Mean Squared Error (MSE)**：

$$\text{similarity} = \cos(u, v)$$

- 流程圖:
```
Sentence A → BERT → Pooling → u ─┐
                                  ├→ cosine-sim(u, v) → [-1 … 1]
Sentence B → BERT → Pooling → v ─┘
```

---

### 三、Triplet 架構 + Triplet 目標函數

- 適用資料：三元組資料（anchor / positive / negative）

- 給定核心句子 $a$、正面句子 $p$、負面句子 $n$，調整網路使 $a$ 與 $p$ 的距離小於 $a$ 與 $n$ 的距離：

$$\min \max(\|s_a - s_p\| - \|s_a - s_n\| + \varepsilon, \ 0)$$

$$距離度量：**Euclidean Distance**$$

$$邊際 \varepsilon = 1，確保 s_p 比 s_n 至少更靠近 s_a 且距離至少為 \varepsilon$$

---

## 肆、相似度比較與評估

### 一、評估指標

Paper 採用 **Spearman 相關性 $\rho$**（而非 Pearson），因為 Pearson 相關性對 STS 任務有缺陷。

### 二、語意文本相似度 (STS) 結果

| Model | Avg. Spearman $\rho$ |
|---|---|
| Avg. GloVe embeddings | 61.32 |
| Avg. BERT embeddings | 54.81 |
| BERT CLS-vector | 29.19 |
| InferSent - GloVe | 65.01 |
| Universal Sentence Encoder | 71.22 |
| **SBERT-NLI-base** | **74.89** |
| **SBERT-NLI-large** | **76.55** |

> BERT 直接輸出的向量效果甚至比 GloVe embeddings 還差，說明 BERT 的向量空間並不適合直接用 cosine similarity 比較。

### 三、論點相似度 (AFS) 評估

- AFS 語料庫：從社群媒體標註 6000 個論點句子對，涵蓋 **槍枝管制**、**同性婚姻**、**死刑**三個主題

- 標註尺度 0（不相關）～ 5（完全相同）。

| 情境 | 結果 |
|---|---|
| **10-fold 交叉驗證** | SBERT 表現與 BERT 近乎相等 |
| **跨主題評估** | SBERT 在 Spearman 相關性上下降約 **7 點** |

- **跨主題表現較差的原因：**

BERT 能直接逐字比較兩個句子（Cross-Encoder），而 SBERT 需要將未知主題的句子映射到向量空間，對更多主題的訓練需求更高。

---

## 伍、計算複雜度比較：BERT vs. SBERT

- 在 10,000 個句子中，尋找最相似的句子對：

| 方法 | 所需時間 |
|---|---|
| BERT / RoBERTa | ~65 小時（約 5000 萬次推論） |
| **SBERT** | **~5 秒** |

- BERT 需要計算 $\frac{n(n-1)}{2} = 49,995,000$ 次推論，而 SBERT 只需計算 10,000 個句子嵌入，再做 cosine similarity 比較（~0.01 秒）。

---

## 陸、過去方法的問題（BERT / RoBERTa）

| | BERT（Cross-Encoder） |
|---|---|
| **優勢** | 在 Sentence-Pair Regression 任務（比如: STS）表現出色 |
| **缺點** | 兩個句子都需餵入網路，計算開銷龐大，不適用於 Clustering、大規模語意搜尋任務 |

---
