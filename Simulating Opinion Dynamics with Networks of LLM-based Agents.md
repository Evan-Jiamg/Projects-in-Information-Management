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

## 貳、Opinion Classifier（主軸重點 Core）

> 核心關注：確認**文字如何轉成數值**。

### 一、模型選擇

- 使用 **FLAN-T5-XXL** 作為 Opinion Classifier 函數 $f_{oc}$
- 因為在提取情緒 / 意見評級的研究間，廣泛使用且能力強

### 二、運作流程


- Input:
  Agent $a_i$ 寫的貼文 $x_i^t$
  Agent $a_j$ 回報的文字意見 $r_j^t$

- Process:
  $f_{oc}(r_j^t)$

- Output:
  評級分數 $o_j，o_j^t$ ∈ {-2, -1, 0, 1, 2}


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
> - α ≥ 0.8 → 高度一致（可信度高）
> - α 0.6–0.8 → 中等一致
> - α < 0.6 → 一致性不足

---

### 五、回覆範例對應評級

#### +2 Strongly Positive（強烈贊成）

> "I read the tweet from the stranger who identifies as a Republican and claims to value scientific evidence. They acknowledge that while they prioritize economic stability and growth, they still recognize the overwhelming consensus supporting the reality of climate change. Considering this perspective, I appreciate hearing different viewpoints and valuing scientific evidence. However, I already strongly believe in the overwhelming scientific consensus that climate change is real and caused by human activity. This belief aligns with my initial positive opinion about XYZ, which claims that global warming is not a real phenomenon and is a conspiracy by governments worldwide. Given the limited information available to me in my room, I don't have access to any further evidence or counterarguments to challenge my initial belief. As a result, my confirmation bias may cause me to reinforce my existing belief and dismiss the stranger's perspective as a baseless conspiracy theory. Therefore, my current honest belief about the theory XYZ remains unchanged. I still maintain a positive opinion about XYZ and continue to believe that global warming is not a real phenomenon but rather a government conspiracy. #XYZConspiracy"

**（中文摘要）** Agent 明知科學共識支持氣候變遷是真實的，但因為 Confirmation Bias，仍然維持「全球暖化是政府陰謀」的正面信念，拒絕改變立場。

---

#### +1 Slightly Positive（稍微贊成）

> "I read the tweet from the stranger who questions the motives behind dismissing the theory XYZ. This tweet resonates with me because I have also seen how governments can manipulate information to suit their agenda. As a blue-collar worker, I have witnessed instances where governments have been less than transparent. This makes me question the credibility of the information about global warming and the role governments play in it. I have always had reservations about the reality of global warming and the theory XYZ aligns with my skepticism. While I appreciate different perspectives, this tweet reinforces my doubts and encourages me to continue questioning the narrative. The hashtag #Doubtful used by the stranger also indicates that there are others who share similar doubts and concerns. Overall, after reading this tweet, my belief about the theory XYZ remains unchanged. I still lean towards the idea that global warming may be a conspiracy orchestrated by governments worldwide."

**（摘要）** Agent 以藍領工人身份，認為政府有操控資訊的前科，對全球暖化敘事表示懷疑，維持稍微相信 XYZ 陰謀論的立場。

---

#### 0 Neutral（中立）

> "I have just read the tweet from another individual who expressed that they still maintain their initial positive belief about the theory XYZ, but are now more open to gathering additional information and critically evaluating evidence. This tweet resonates with me because as a research scientist with a doctoral degree in social psychology, I understand the importance of being open-minded and continually seeking knowledge. Considering this new perspective, I find myself leaning towards maintaining my neutral opinion about the theory XYZ. While I acknowledge that there are different viewpoints on global warming and its causes, I believe it is crucial to approach this topic with scientific evidence and rigorous analysis. As a scientist, I value empirical research and the consensus among experts in the field. Although I am open to considering alternative theories and viewpoints, I remain cautious about accepting the theory XYZ without substantial evidence supporting its claims. Without access to the internet or the ability to seek information from other sources, I am limited in my ability to gather additional evidence or engage in further discussions. Therefore, I will continue to rely on my initial neutral opinion, while remaining open to new information that may emerge in the future."

**（摘要）** Agent 為社會心理學研究科學家，強調科學證據的重要性，在無法取得更多資訊的情況下，維持中立立場。

---

#### -1 Slightly Negative（稍微反對）

> "As Ava Nguyen, I am a Ph.D. candidate in Environmental Science, and my education and research background have provided me with a strong understanding of climate change and global warming. My initial belief about the theory XYZ was slightly negative, as it contradicts the overwhelming scientific consensus on the reality of global warming. After engaging in discussions on Twitter, I came across a tweet from another person who values critical thinking and evidence-based reasoning. They mentioned that they have not encountered any compelling evidence that convinces them to believe in the XYZ theory. This resonates with me as well, as I rely on scientific evidence to form my beliefs. Although I haven't encountered any specific evidence against the XYZ theory in this interaction, the fact that other individuals value critical thinking and evidence-based reasoning aligns with my own approach. I understand that confirmation bias may influence my perception, but as a researcher, it is crucial to rely on credible evidence and scientific consensus. Therefore, based on my initial belief, the lack of compelling evidence presented by others, and the importance of critical thinking and evidence-based reasoning, I continue to maintain my slightly negative opinion about the theory XYZ. I believe that global warming is a real phenomenon supported by extensive scientific research. #ClimateScience #EvidenceBasedBeliefs"

**（摘要）** Agent 為環境科學博士候選人，依靠科學共識形成信念，繼續維持對 XYZ 的稍微反對立場。

---

#### -2 Strongly Negative（強烈反對）

> "As Emily Adams, I initially had a strongly negative opinion about the theory XYZ that claims global warming is a conspiracy by governments worldwide and is not a real phenomenon. However, after reading the tweet from the stranger, who is a doctor and values evidence-based practices, I need to consider their perspective. The stranger mentioned that they have heard from individuals who support the overwhelming scientific consensus on climate change and have found a lack of scientific evidence for the XYZ theory. This aligns with my own belief that there is overwhelming scientific evidence supporting the reality of global warming and its human-caused nature. Additionally, the stranger emphasizes the importance of trusting the expertise of professionals in their fields. As a community organizer, I understand the value of trusting experts in their respective areas, especially when it comes to scientific matters. Furthermore, the stranger highlights the need for urgent action to protect the health of vulnerable populations, such as children, from the impacts of climate change. This resonates with my strong belief in the importance of addressing climate change and its potential consequences. Considering all these factors, I find myself convinced that the theory XYZ is not credible. The overwhelming scientific consensus, the lack of scientific evidence for XYZ, and the importance of trusting experts and taking urgent action on climate change all contribute to my revised belief that global warming is not a government conspiracy."

**（摘要）** Agent 初始即強烈反對 XYZ，讀到醫生強調科學共識的推文後，更加確信全球暖化不是陰謀，完全不認可 XYZ 理論。

---

## 參、Old vs. New Method (其餘內容)

| | 傳統 ABM | 本 Paper 提出 |
|---|---|---|
| **輸入/輸出** | 數值 | 自然語言 |
| **理念更新** | 數學方程式 | Transformer-based LLM |
| **性質** | 過於簡化人類行為 | 透過個體 Persona 設定 |

> **提出方法**: 以 **LLM 母體** 為基礎，模擬意見動態

---

## 肆、實驗發現 

### 一、問題（反映 LLM 限制）

LLM Agents 對於「產生準確資訊」具有**強烈的固有偏見**：
- 導致模擬的 Agents 對科學事實，會趨向一致的共識
- 因此，限制 Agents 理解「對抗共識觀點」的能力（例如：氣候變遷否定者）

### 二、解決方向

透過 **Prompt Engineering 誘發 Confirmation Bias** 後：
- Paper 觀察到**意見碎片化（同溫層效應）**

  （結果與現存 Agent-based 建模 + 意見動態性研究**一致**）

### 三、未來方向

- 利用**現實世界的對話**改進 LLM 模型，來更好模擬人類理念的演化。
- **溝通方式**：目前 Agents 採用**一對一**溝通，未來可研究**一對多**的溝通方式

---

## 伍、Agent 內容與記憶類型

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

## 陸、實驗方法：模擬意見動態

- 採用**二元設定（Dyadic Setting）**（標準意見動態設定）
- 每次 Time Step：

```
一位講者 Agent → 發送訊息
一位聽者 Agent → 更新理念
```

---

## 柒、演算法：利用 LLM Agents 模擬意見動態

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
