# 文件切塊（chunking）

## 為什麼要切
1. 讓內容放進 embedding 模型的 context window；超過的 token 會被截斷丟掉
2. chunk 要自己有意義：沒有脈絡的句子可能搜不到
- 經驗法則：若一段文字在沒有周圍脈絡時人也看得懂，語言模型通常也看得懂
- 太小或太大都會使搜尋不精確
- 長 context 模型也仍需切：大 chunk 增加延遲與成本，且有 lost-in-the-middle 問題

## 切法（Pinecone）
| 方法 | 做法 |
|---|---|
| Fixed-size | 固定 token 數，常取 embedding 模型上限；**建議先從這裡開始** |
| 句子／段落 | naive、NLTK、spaCy |
| Recursive character | LangChain 依 "\n\n"、"\n"、" "、"" 依序切 |
| 文件結構 | PDF、HTML、Markdown、LaTeX 依結構切 |
| Semantic | 句子分組做 embedding，語意距離大處為邊界（實驗性） |
| Contextual（LLM） | 為 chunk 加上說明再 embed，見 [contextual-retrieval](../techniques/contextual-retrieval.md) |

## 找最佳切法
測一個範圍（128/256 抓細節，512/1024 留脈絡），用代表性資料與查詢比較；可用 chunk expansion 在檢索後取相鄰 chunk。沒有放諸四海皆準的切法。

## 研究缺口
Barnett 指出：啟發式與語意切塊的取捨，仍需系統性評估（raw/04 §6.1）。

來源：raw/02, raw/04
