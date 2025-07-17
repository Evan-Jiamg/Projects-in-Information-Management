# 1. 大標題
-> `#`
## 中標題
-> `##`
### 小標題
-> `###`

---
# 2. 文本樣式
- 粗體 `**` or `__` -> **粗體字** or __粗體字__
- 斜體 `*` or `_` -> *斜體字* or _斜體字_
- 刪除線 `~` or `~~` -> ~錯字~ or ~~錯字~~
- 粗體 `**` or `__` + 關鍵字斜體 `*` -> **粗體字內的*關鍵字*呢** or __粗體字內的*關鍵字*呢__
- 全部粗體 + 斜體 `***` -> ***重要文本***
- 底線字 `<ins>`...`<ins/>` -> 這是<ins>底線字<ins/>
- 上標字 `<sub>`...`<sub/>` -> 這是<sub>上標<sub/>字
- 下標字 `<sup>`...`<sup/>` -> 這是<sup>下標<sup/>字

---
# 3. 引用
## 3.1 引用文本 Quotation `>`
This is not a quote.
> This is a quote.

## 3.2 引用 Code '`'
- 使用快捷鍵 -> **Ctrl + e**
- 文字內引用 Code -> '`'<br>
  Use `git status` to list all new or modified files that haven't yet been committed.
- 獨立區塊引用 Code -> '```'<br>
  Some basic Git commands are:
  ```
  git status
  git add
  git commit
  ```

## 3.3 引用 Issues 或 Pull requests
- 輸入 `#` + `編號`，例如：`#123`，會自動變成指向該 GitHub 的 Isssues 或 Pull requests 超連結。
- 範例: 修復請參考 #123


---
# 4. 字體顏色

Color  語法          範例<br>
HEX	  `#RRGGBB`      `#0969DA`<br>
RGB	  `rgb(R,G,B)`   `rgb(9, 105, 218)`<br>
HSL	  `hsl(H,S,L)`   `hsl(212, 92%, 45%)`<br>

範例 -> The background color is `#ffffff` for light mode and `#000000` for dark mode.

---
# 5. 超連結 (MarkDown 連結: 必須寫在同一行，跨行會失敗!!!)
- **快捷鍵**
  -> **Ctrl + k**
## 5.1 網頁超連結 
  語法: `[顯示文字](網址)<br>`
  範例: This site was built using [GitHub Page](https://pages.github.com/)<br><br>
## 5.2 章節超連結
  語法: `[顯示文字](#章節標題名稱)`<br>
  作用: 將連結指向同個 .md 檔案中的***標題***，點選標題<ins>左邊出現的*小鏈結*圖示<ins/>即可<br>
  範例: 指向[大標題位置](#大標題)<br><br>
## 5.3 重複章節標題 vs. 章節超連結
  範例：<br>
  \## This heading is not unique in the file<br>
  \## This heading is not unique in the file<br>
  \## This heading is not unique in the file<br>
  GitHub 會將重複標題名稱以標號區別：<br>
  \[顯示文字](# this-heading-is-not-unique-in-the-file)<br>
  \[顯示文字](# this-heading-is-not-unique-in-the-file-1)<br>
  \[顯示文字](# this-heading-is-not-unique-in-the-file-2)<br><br>
## 5.4 相對連結 Relative Link
  語法: `[顯示文字](docs/完整檔案名稱)`<br>
  作用: 用於連結同一個 Repository 中的其他檔案<br>
  優點: 適用於 GitHub Pages、多人協作<br>
  範例: [ML Spring 2021] (docs/類神經網路訓練不起來怎麼辦 (三).md)<br><br>

    | 用途                                         | 說明                                                         | 實例                                                                                         |
    | ------------------------------------------ | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
    | 在同分支的 `.md` 檔                           | 若你在 `main` 分支裡的某個 Markdown 檔內，想連到 `main` 分支中某個檔案（例如圖片）     | `/assets/images/electrocat.png` <br> 👉 表示從目前 `.md` 所在位置開始往下找                              |
    | 在另一分支的 `.md` 檔                           | 你目前在某個分支（例如 `dev`），想連到 `main` 分支中的圖片檔案                     | `/../main/assets/images/electrocat.png` <br> 👉 `..` 表示跳出當前目錄，再進入 `main` 分支資料夾             |
    | 在 `issue、pull request、comment` 中使用的連結      | 這些是討論區域，非 Markdown 頁面，需完整指向 blob 路徑並加上 `?raw=true` 來正確顯示圖片 | `../blob/main/assets/images/electrocat.png?raw=true` <br> 👉 `blob` 表示原始檔案內容，不是編輯器介面       |
    | 在另一 `repository` 的 `.md` 檔                 | 假設你從 `userA/projectB` 想連到 `github/docs` 這個儲存庫的圖片           | `/../../../../github/docs/blob/main/assets/images/electrocat.png` <br> 👉 往上跳出幾層，然後進入別的儲存庫 |
    | 在另一 `repository` 的 `issue` 或 `pull request` 中 | 和上面一樣，但這是在 GitHub 討論區中使用，要加 `?raw=true` 才能正確載入             | `../../../github/docs/blob/main/assets/images/electrocat.png?raw=true`                     |

  
  ## 5.5 手動產生定位點
  - 語法: 生成定位點 -> `<a name="自定義名稱"></a>` <br>
        呼叫定位點 -> `[顯示文字](#自定義名稱)`<br><br>
  - 作用: 跳轉到特定段落，且不想 or 不能使用標題來產生自動錨點<br>
  - 範例:<br>
    <a name="here"><\a>
    我想要跳至這個段落，但我並不想要使用 _標題_
  
    [手動生成的定位點](#here)

---
# 6. 換行方式
| 方法                  | 範例                          | 顯示結果             |
| ------------------- | --------------------------- | ---------------- |
| **1. 結尾加兩個空格**（最常用） | `Hello␣␣<Enter>`<br>`World` | 會換行              |
| **2. 結尾加反斜線 `\`**   | `Hello\<Enter>`<br>`World`  | 會換行              |
| **3. 用 `<br/>` 標籤** | `Hello<br/>World`           | 會換行，適用 HTML 嵌入情境 |

範例:
  1. Hello  
     World
  3. Hello\
     World
  4. Hello<br/>World

---
# 7. 插入圖片
- 語法 (一般連結): `![圖片名稱](圖片網址)`\
- 語法 (相對連結): `![圖片名稱](docs/完整檔案圖片名稱)`
  [用於連結同一個 Repository 中的其他檔案]<br>
- 上傳圖片渠道: \
  可在 Issue、PR、Comment、或 .md 中直接：
  1. 拖曳檔案
  2. `Ctrl + V` 貼上圖片
  3. 手動選擇檔案
- 範例: \
  ![Image Alt Text](https://miro.medium.com/v2/resize:fit:1400/1*JLYlSLSK8-AZo8gt9UdYqA.jpeg)

---
# 8. 清單
## 8.1 無序清單
語法:`-` or `*` or `+`\
舉例:\
  - George Washington -> '- George Washington'
  * John Adams -> '* John Adams'
  + Thomas Jefferson -> '+ Thomas Jefferson'
## 8.2 有序清單
語法: `1.`、`2.`...\
舉例:\
  `1. James Madison`\
  `2. James Monroe`\
  `3. John Quincy Adams`\
## 8.3 巢狀清單
### Type 1
1. A
   - A-1
     - A-1-1
2. B
### Type 2
- A
  - B
    - C
## 8.4 任務清單
- 若要建立工作清單，請在清單項目前加入: \
    任務未完成 ->`[ ]`\
    任務完成 -> `[X]`\
    
- 舉例:
  - [x] #739
  - [ ] https://github.com/octo-org/octo-repo/issues/740
  - [ ] Add delight to the experience when all tasks are complete :tada:
 
---
# 9. 提及相關人員
- 語法: `@人員名稱`
- 作用: 會*觸發通知*，並提醒相關人員注意對話。
- 舉例:\
  @github/support What do you think about these updates?

---
# 10. 輸入表情符號
- 語法: `:emoji_code:`
- **All GitHub Emoji Code:**\
  [Review All Emoji](https://gist.github.com/rxaviers/7360908)
- 範例:\
  Looks great! :tada:  ->  +1: Looks great! `:tada:`\
  OMG~~ :blush: -> OMG~~ `:blush:`

---
# 11. 註解
- 語法: `<!--`...`-->`
- 範例:\
  `<!-- 註解的的內容` -->`

---
# 12. 註腳
- 語法：\
  這是一個腳註[^1]。
  [^1]: 腳註內容。
- 作用:\
  1. **腳註**會自動集中呈現在**文章底部**。
  2. **支援多行**（用***兩個空格*** 開頭來斷行）。

---
# 13. 重點提示框
- 語法:
  1. 注意   -> `[!NOTE]`
  2. 小技巧 -> `[!TIP]`
  3. 很重要 -> `[!IMPORTANT]`
  4. 警告   -> `[!WARNING]`
  5. 小心   -> `[!CAUTION]`
     
- 範例:
  > [!NOTE]
  > Useful information that users should know, even when skimming content.
  
  > [!TIP]
  > Helpful advice for doing things better or more easily.
  
  > [!IMPORTANT]
  > Key information users need to know to achieve their goal.
  
  > [!WARNING]
  > Urgent info that needs immediate user attention to avoid problems.
  
  > [!CAUTION]
  > Advises about risks or negative outcomes of certain actions.

---
# 14. 忽略 Markdown 格式
- 語法: `\`
- 作用: 透過在 Markdown 的 KEYWORD 前面輸入 `\`，可以避免變成 Markdown 格式
- 範例:\
  `Let's rename \*our-new-project\* to \*our-old-project\*.`
  變成 -> Let's rename \*our-new-project\* to \*our-old-project\*.
  

