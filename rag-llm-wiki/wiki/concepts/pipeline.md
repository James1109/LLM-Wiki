# RAG 的兩段流程

## Index（索引，開發時／文件更新時）
1. 文件預處理（影片需先轉錄，如 Whisper）
2. 切成 chunk
3. embedding 模型轉成向量（一串數字，壓縮的語意表示）
4. 把 chunk 與向量存入資料庫（向量資料庫）

## Query（查詢，執行時）
1. （可選）Query rewriter：用聊天紀錄把模糊問題改寫成完整問題
2. 問題轉成 embedding，取 top-k 相似 chunk（如 cosine 相似度）
3. （可選）Re-rank：讓答案所在 chunk 排在前面
4. Consolidator：因 token 與速率限制，決定哪些 chunk 進入 context
5. Reader / 生成：LLM 過濾雜訊、遵循格式、產出答案

## 設計抉擇
- chunk 太小，某些問題答不了；太長，答案含雜訊
- **更換 embedding 策略需重新索引全部 chunk**
- 傳統預處理的 chunk 通常「不超過幾百 tokens」（raw/03）

## 不一定要 RAG
知識庫小於 200,000 tokens（約 500 頁）時，可整份放進 prompt（raw/03）。

來源：raw/03, raw/04
