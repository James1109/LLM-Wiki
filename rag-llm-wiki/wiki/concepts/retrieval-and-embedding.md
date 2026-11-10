# 檢索與向量

## 向量檢索（語意）
- 論文的檢索器為 DPR bi-encoder：查詢與文件各有 BERT-base 編碼器，相關度為向量內積，用 MIPS 取 top-K（raw/01）
- 實務上常用 cosine 相似度；直覺是「語意接近的 chunk 可能包含答案」（raw/04）

## BM25（關鍵字）
- 詞彙比對的排序函式，建立在 TF-IDF，考慮文件長度、對詞頻做飽和（raw/03）
- 優勢：精確字串。例：查「Error code TS-999」，embedding 可能只找到一般錯誤碼，BM25 能比對確切字串
- 論文也發現：FEVER 任務（以實體為主）上 BM25 表現最好，其他任務學習式檢索更佳（raw/01）

## 混合檢索
embedding 找語意 + BM25 找精確詞 → rank fusion 合併去重 → 取 top-K 放進 prompt。Anthropic 測試中 Embeddings+BM25 優於單用 embedding（raw/03）。

來源：raw/01, raw/03, raw/04
