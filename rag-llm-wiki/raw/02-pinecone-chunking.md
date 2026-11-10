# 02 Pinecone — Chunking Strategies for LLM Applications
- 來源：https://www.pinecone.io/learn/chunking-strategies/ ；作者 Roie Schwaber-Cohen、Arjun Patel；頁面顯示日期 Jun 28, 2025
- 讀取日期：2026-11-10｜**已讀範圍：原頁全文**｜廠商（向量資料庫）文件
- ⚠️ 早先讀到的第三方摘要寫「起點 256–512 tokens、1024 最佳」，**原頁沒有這些說法**，已不採用

## 為什麼要切塊
- 兩大理由：(1) 讓內容能放進 embedding 模型的 context window，超過的 token 會被截斷丟掉；(2) chunk 本身要包含可供搜尋的資訊，沒有脈絡的句子可能搜不到
- 經驗法則：若一段文字在沒有周圍脈絡時，人也看得懂，語言模型通常也看得懂
- chunk 太小或太大，都可能造成搜尋不精確或漏掉相關內容
- 長文件 embedding 會引入雜訊、稀釋個別句子的意義；單句 embedding 聚焦於該句語意

## 長 context 模型下仍需切塊
- 即使 200k context 能放下整份文件，大 chunk 仍會增加延遲與成本
- 長 context 有 lost-in-the-middle 問題：埋在長文件中的相關資訊會被漏掉

## 選擇策略時的 4 個問題
資料類型？用哪個 embedding 模型？查詢多長多複雜？檢索結果怎麼用（搜尋、QA、RAG、agent）？

## 方法
- Fixed-size：決定每塊 token 數，常用 embedding 模型的 context 上限（頁面舉例：llama-text-embed-v2 為 1024、text-embedding-3-small 為 8196）；**文中建議多數情況先從這裡開始，不夠再改**
- Content-aware：句子／段落切分（naive、NLTK、spaCy）、LangChain 的 RecursiveCharacterTextSplitter（預設分隔符 "\n\n"、"\n"、" "、""）、依文件結構（PDF、HTML、Markdown、LaTeX）
- Semantic chunking：由 Greg Kamradt 提出的實驗性做法；句子分組做 embedding，比較相鄰群組的語意距離，距離大處即為邊界
- Contextual chunking with LLMs：引用 Anthropic 2024 的 contextual retrieval，用 LLM 為 chunk 產生說明再 embed，並以快取避免重複處理文件

## 找最佳切法
- 測一個範圍：小 chunk（如 128 或 256 tokens）抓細節，大 chunk（如 512 或 1024 tokens）保留脈絡
- 用代表性資料，多個 index 或多個 namespace，跑一批查詢比較品質，反覆迭代
- Chunk expansion：檢索後再取相鄰 chunk（可擴到段落、頁、整份文件），兼顧低延遲與脈絡
- 結語：沒有放諸四海皆準的切法
