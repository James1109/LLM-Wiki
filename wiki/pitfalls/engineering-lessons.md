# 工程經驗與限制

## Barnett 等人的經驗（三個案例）
- 兩個核心結論：(1) RAG 系統只有在運作中才能真正驗證；(2) 穩健性是演化出來的，不是一開始設計好
- 需要持續校準：chunk 大小、embedding 策略、chunking 策略、檢索策略、consolidation 策略、context 大小、prompt
- 在檢索內容加入檔名與 chunk 編號等 metadata，有助檢索與抽取
- AI Tutor 中更大的 context（8K vs 4K）結果較好
- Semantic cache 可降低成本與延遲
- 微調過的 LLM 要測越獄，因為微調可能逆轉安全訓練
- 離線評估（如 G-Eval）需要有標註的問答資料

## Lewis 等人的觀察
- Retrieval collapse：某些任務（如故事生成）檢索器退化，生成器忽略文件
- 外部知識來源（如 Wikipedia）不會完全符合事實且不帶偏見

## 資料來源的立場
- 簡報的比較資料有廠商文章（Pinecone、Progress）與自家測試（Anthropic），結論應視為「該來源的說法」
- Barnett 論文是經驗報告，不是對照實驗；其 BioASQ 評估由非領域專家人工檢查

來源：raw/01, 03, 04, 05
