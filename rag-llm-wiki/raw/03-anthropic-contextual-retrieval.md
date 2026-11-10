# 03 Anthropic — Introducing Contextual Retrieval
- 來源：https://www.anthropic.com/research/contextual-retrieval （抓取網址 engineering/contextual-retrieval 轉址至此）；發佈 2024-09-19；團隊 Engineering；頁面最後修改 2026-10-18
- 讀取日期：2026-11-10｜**已讀範圍：全文**｜官方工程文章（自家方法與自家測試）

## RAG 前導
- 傳統 RAG 預處理：(1) 切成最多幾百 tokens 的 chunk；(2) embedding 模型轉成向量；(3) 存入向量資料庫。執行時以語意相似度取回最相關 chunk 加入 prompt
- BM25：詞彙比對的排序函式，建立在 TF-IDF 上，考慮文件長度並對詞頻做飽和；擅長精確字串。例：查「Error code TS-999」，embedding 可能只找到一般錯誤碼內容，BM25 能比對到確切字串
- 結合兩者：embedding 找語意、BM25 找精確詞，再用 rank fusion 合併去重，取 top-K 放進 prompt
- 問題：傳統 RAG 切塊後會「破壞脈絡」。例：chunk 寫「The company's revenue grew by 3% over the previous quarter.」，看不出是哪家公司、哪一季

## 方法
- Contextual Retrieval = Contextual Embeddings + Contextual BM25：為每個 chunk 在 embedding 與建 BM25 索引前，先加上 chunk 專屬的說明
- 範例：加上「This chunk is from an SEC filing on ACME corp's performance in Q2 2023; ...」
- 以 Claude 3 Haiku 的 prompt 產生，內容為整份文件＋該 chunk，要求「簡短說明此 chunk 在整份文件中的脈絡」；產生的說明通常 50–100 tokens
- 其他類似做法：通用文件摘要加到 chunk（作者實驗後增益很有限）、HyDE、summary-based indexing（作者評估後表現低）
- 成本：用 prompt caching，文件只需載入快取一次。假設 800 token chunk、8k token 文件、50 token 指令、每 chunk 100 token 說明，一次性成本 **$1.02 / 每百萬文件 token**
- 先前還說明 prompt caching 可降低延遲超過 2 倍、成本最多 90%

## 實驗與結果
- 領域：程式碼、小說、ArXiv 論文、科學論文；最佳 embedding 設定（Gemini Text 004），取 top-20 chunk
- 指標：1 − recall@20（相關文件沒被取到 top-20 的比例）
- Contextual Embeddings：失敗率降 35%（5.7% → 3.7%）
- ＋Contextual BM25：降 49%（5.7% → 2.9%）
- ＋Reranking：降 67%（5.7% → 1.9%）

## Reranking
- 步驟：初步檢索取前 150 個 → 連同問題送入 reranking 模型 → 為每個 chunk 打分，選 top-K（用 20）→ 作為 context 生成
- 測試用 Cohere reranker；Voyage 也有但沒時間測
- 取捨：多一步執行時延遲與成本，要在效果與延遲間平衡

## 實作考量與結論
- Chunk 邊界、大小、重疊都會影響；embedding 模型中 Gemini 與 Voyage 特別有效；可為領域客製 prompt；放入 20 個 chunk 比 5 或 10 個好，但需自行實驗
- 結論：Embeddings+BM25 優於單用 embedding；加 context、加 reranking 都更好；**效益可疊加**；「Always run evals」
- **小知識庫替代方案**：知識庫小於 200,000 tokens（約 500 頁）時，可整份放進 prompt，不需要 RAG
