# Reranking（重排序）

- 為什麼：大型知識庫初步檢索常回傳上百個相關度不一的 chunk；只把最相關的交給模型，答案較好、成本與延遲較低
- 步驟（Anthropic）：初步檢索取前 150 → 連同問題送入 reranking 模型 → 給每個 chunk 打分 → 選 top-K（用 20）→ 作為 context
- 測試用 Cohere reranker（Voyage 也有，未測）
- 取捨：執行時多一步，增加少量延遲與成本；重排越多越準，也越慢越貴
- 對應失敗點：FP2（答案在文件中但排名不夠前）

來源：raw/03, raw/04
