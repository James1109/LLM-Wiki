# Contextual Retrieval

- Anthropic，2024-09-19；Contextual Embeddings + Contextual BM25
- 問題：切塊後單一 chunk 缺脈絡。例：「The company's revenue grew by 3% over the previous quarter.」不知哪家公司、哪一季
- 做法：在 embedding 與建 BM25 索引前，為每個 chunk 先加上說明，如「This chunk is from an SEC filing on ACME corp's performance in Q2 2023; ...」
- 說明由 LLM（文中用 Claude 3 Haiku）根據整份文件與該 chunk 產生，通常 50–100 tokens
- 用 prompt caching 讓文件只載入一次；文中估算一次性成本 $1.02 / 每百萬文件 token（假設 800 token chunk、8k token 文件）

## 結果（top-20 檢索失敗率，1 − recall@20）
| 設定 | 失敗率 | 相對基線 |
|---|---|---|
| 一般 embedding | 5.7% | — |
| Contextual Embeddings | 3.7% | 降 35% |
| ＋Contextual BM25 | 2.9% | 降 49% |
| ＋Reranking | 1.9% | 降 67% |

## 註記
- 資料為 Anthropic 自己的測試：領域含程式碼、小說、ArXiv 與科學論文；最佳 embedding 為 Gemini Text 004
- 通用文件摘要加到 chunk：作者實驗增益很有限
- 結論：效益可疊加；放 20 個 chunk 優於 10 或 5 個；always run evals
- Pinecone 將此列為「Contextual Chunking with LLMs」

來源：raw/03, raw/02
