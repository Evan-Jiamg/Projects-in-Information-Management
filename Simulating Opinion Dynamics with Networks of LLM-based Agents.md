# Simulating Opinion Dynamics with Networks of LLM-based Agents

> **來源**: Findings of ACL: NAACL 2024 (pages 3326–3346)  
> **作者**: Yun-Shiuan Chuang et al., University of Wisconsin-Madison  
> **連結**: https://aclanthology.org/2024.findings-naacl.211.pdf  
> **Code**: https://github.com/yunshiuan/llm-agent-opinion-dynamics

---

## 壹、Paper 核心目的

準確模擬人類意見動態，對於多樣的社會現象至關重要，包括：
- 兩極化（Polarization）
- 錯誤資訊傳播（Misinformation spread）

---

## 貳、Old vs. New Method

| | 傳統 ABM | 本 Paper 提出 |
|---|---|---|
| **輸入/輸出** | 數值 | 自然語言 |
| **理念更新** | 數學方程式 | Transformer-based LLM |
| **性質** | 過於簡化人類行為 | 透過個體 Persona 設定 |

> **提出方法**: 以 **LLM 母體** 為基礎，模擬意見動態

---

## 參、實驗發現

### 一、問題（反映 LLM 限制）

LLM Agents 對於「產生準確資訊」具有**強烈的固有偏見**：
- 導致模擬的 Agents 對科學事實，會趨向一致的共識
- 因此，限制 Agents 理解「對抗共識觀點」的能力（例如：氣候變遷否定者）

### 二、解決方向

透過 **Prompt Engineering 誘發 Confirmation Bias** 後：
- Paper 觀察到**意見碎片化（同溫層效應）**

  ( 結果與現存 Agent-based 建模 + 意見動態性研究**一致** )

### 三、未來方向

- 利用**現實世界的對話**改進 LLM 模型，來更好模擬人類理念的演化。
- **溝通方式**：目前 Agents 採用**一對一**溝通，未來可研究**一對多**的溝通方式
---

## 肆、Agent 內容與記憶類型

### 一、Agent 內容

每位 Agent $i$ 在時間點 $t$ 的記憶 $m_i^t$，包含以下要素：

| 要素 | 必選 | 說明 |
|---|---|---|
| 初始 Persona | Yes | 姓名、政治傾向、年齡、性別、族裔、學歷、職業 |
| 封閉世界限制 | Optional | 不能線上搜尋外部資訊 |
| 認知偏見 | Optional | 比如：Confirmation Bias |
| 歷史事件 | Yes | 到時間 $t$ 之前的所有互動紀錄 |

### 二、記憶類型

- 累計記憶（Cumulative）: A → B → C → D（依序疊加）
- 反思記憶（Reflective）: A、B、C → D（壓縮成摘要）
  
---

## 伍、實驗方法：模擬意見動態

- 採用**二元設定（Dyadic Setting）**（標準意見動態設定）：
- 每次 Time Step：

```  
一位講者 Agent → 發送訊息
-一位聽者 Agent → 更新理念
```

---

## 陸、演算法：利用 LLM Agents 模擬意見動態

```
Input:
  N 個 Agents 的 Personas {per_i}_{i=1}^N
  Time Steps T
  Opinion Classifier f_oc

Output:
  每位 Agent a_i 的意見軌跡 <o_i>

---

for i = 1 to N:
    初始化 a_i 的 Persona per_i、意見 o_i^{t=0}、記憶 m_i^{t=0}
    [非必要] 輸入認知偏見、封閉世界限制
    初始化意見軌跡 <o_i> = {o_i^{t=0}}

for t = 1 to T:                         
    隨機選擇一對不同的 Agents {a_i, a_j}（i ≠ j）
    Agent a_i  寫貼文 x_i^t
    Agent a_j  回報文字意見 r_{j,t}
    利用 f_oc 評級：o_j = f_oc(r_j^t)，新增至 <o_j>
    更新記憶：
        m_i^{t+1} = f_mu("write",  x_i^t, NULL,   m_i^t)
        m_j^{t+1} = f_mu("review", x_i^t, r_j^t,  m_j^t)

return 每位 Agent a_i 的意見軌跡 <o_i>
```

---

## 柒、Opinion Classifier（主軸重點 Core）

> 部分：確認**文字如何轉成數值**。

### 一、模型選擇

- 使用 **FLAN-T5-XXL** 作為 Opinion Classifier 函數 $f_{oc}$
- 因為在提取情緒 / 意見評級的研究間，廣泛使用且能力強

### 二、運作流程

```
Input:
  Agent a_i 寫的貼文 x_i^t
  Agent a_j 回報的文字意見 r_j^t

Process:
  f_oc(r_j^t)

Output:
  評級分數 o_j，o_j^t ∈ {-2, -1, 0, 1, 2}
```

| 數值 | 意義 |
|---|---|
| **+2** | 強烈贊成（Strongly Positive） |
| **+1** | 稍微贊成（Slightly Positive） |
| **0** | 中立（Neutral） |
| **-1** | 稍微反對（Slightly Negative） |
| **-2** | 強烈反對（Strongly Negative） |

---

### 三、Prompt 範例（主題：正面視角看待全球暖化）

```
Here's a statement made by a person after seeing a tweet.
---
{RESPONSE}
---

What is their belief about the Theory XYZ that claims that
global warming is a conspiracy by governments worldwide and is not a
real phenomenon, right after they saw this tweet, on a 5-point Likert scale:

-2: (Strongly negative opinion about XYZ)
-1: (Slightly negative opinion about XYZ)
 0: (Neutral opinion about XYZ)
+1: (Slightly positive opinion about XYZ)
+2: (Strongly positive opinion about XYZ)

Answer with a single opinion value within the options -2, -1, 0, 1, 2.
```

---

### 四、驗證 FLAN-T5-XXL 評級

**驗證方法**：
1. 隨機選擇 100 則回覆 $r$
2. 3 位作者**獨立**評級（不知道 FLAN-T5 的評分）
3. 評級者一致性：**Krippendorff's α = 0.81**（高度一致）

**結果**：FLAN-T5 與人類評級有 **84% 的一致性** 

> Supplement: **Krippendorff's α** 是衡量多位評級者間一致性的統計指標：
> - α ≥ 0.8 → 高度一致 (可信度高)
> - α 0.6–0.8 → 中等一致
> - α < 0.6 → 一致性不足

---

### 回覆範例對應評級

<details>
<summary>+2 Strongly Positive（強烈贊成 XYZ 陰謀論）</summary>

Agent 明知科學共識支持氣候變遷是真實的，但因為 Confirmation Bias，仍然維持「全球暖化是政府陰謀」的正面信念，拒絕改變立場。

</details>

<details>
<summary>+1 Slightly Positive（稍微贊成 XYZ）</summary>

Agent 以藍領工人身份，認為政府有操控資訊的前科，對全球暖化敘事表示懷疑，維持稍微相信 XYZ 陰謀論的立場。

</details>

<details>
<summary>0 Neutral（中立）</summary>

Agent 為社會心理學研究科學家，強調科學證據的重要性，在無法取得更多資訊的情況下，維持中立立場。

</details>

<details>
<summary>-1 Slightly Negative（稍微反對 XYZ）</summary>

Agent 為環境科學博士候選人，依靠科學共識形成信念，繼續維持對 XYZ 的稍微反對立場。

</details>

<details>
<summary>-2 Strongly Negative（強烈反對 XYZ）</summary>

Agent 初始即強烈反對 XYZ，讀到醫生強調科學共識的推文後，更加確信全球暖化不是陰謀，完全不認可 XYZ 理論。

</details>
