# 需求摘要

- **對象**：沒學過 RAG、但用過 ChatGPT / Claude 的大學生
- **主題**：RAG（檢索增強生成）是什麼、怎麼運作、哪裡容易出錯
- **形式**：17 頁教學型 HTML 簡報（`slides/index.html`），單檔、可直接瀏覽
- **學習目標**：看完後能
  1. 說出 RAG 解決什麼問題（模型知識有限、難更新、難溯源）
  2. 畫出 RAG 的兩段流程（建索引、查詢時檢索＋生成）
  3. 說明 chunking 為什麼重要，以及 Contextual Retrieval 補了什麼
  4. 分辨 RAG 與微調（補知識 vs 補行為）
  5. 列出 RAG 常見失敗點
- **知識來源**：5 篇資料（Lewis 2020 論文、Pinecone 切塊指南、Anthropic Contextual Retrieval、Barnett 2024 失敗點論文、Progress RAG vs 微調），皆讀全文後整理
- **知識來源限制**：簡報每一頁只能使用 `wiki/` 內的內容，並標註來源頁
- **不做**：不教寫程式、不比較各家產品價格
