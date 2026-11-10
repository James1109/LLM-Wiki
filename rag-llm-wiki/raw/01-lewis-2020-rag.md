# 01 Lewis et al. 2020 — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
- 來源：https://arxiv.org/pdf/2005.11401 （NeurIPS 2020；arXiv v1 2020-05-22，v4 2021-04-12）
- 作者：Patrick Lewis 等 12 人（Facebook AI Research、UCL、NYU）
- 讀取日期：2026-11-10｜**已讀範圍：全文（含附錄）**

## 動機（§1）
- 預訓練模型把知識存在參數裡（parametric），但：難擴充或修改記憶、難說明預測依據、可能產生幻覺
- 結合非參數（檢索式）記憶，知識可直接修改與擴充，被存取的知識可被檢視

## 架構（§2、圖 1）
- 檢索器 p_η(z|x)：DPR，bi-encoder；文件編碼器 BERT-base、查詢編碼器 BERT-base；相關度為向量內積；用 MIPS（最大內積搜尋）取 top-K
- 生成器 p_θ：BART-large（約 400M 參數，附錄 G 寫 406M）；輸入 x 與檢索文件 z 直接串接
- 把檢索到的文件視為潛在變數（latent variable）做邊際化（marginalize）
- RAG-Sequence：整段輸出用同一份文件；RAG-Token：每個 token 可用不同文件
- 訓練：檢索器與生成器聯合訓練，**不需要「該檢索哪份文件」的監督**；文件編碼器與索引固定，只微調查詢編碼器與 BART（§2.4）
- 知識來源：2018 年 12 月 Wikipedia dump，切成不重疊的 100 字 chunk，共 2,100 萬份文件；用 FAISS + HNSW 建索引（§3）
- 訓練時 k ∈ {5, 10}

## 結果
- 開放領域 QA（表 1，Exact Match）：NQ — RAG-Sequence 44.5、RAG-Token 44.1、DPR 41.5、T5-11B（closed-book）34.5
- 作者指出 RAG 不需要重排序器（re-ranker）或抽取式閱讀器就達到最佳成績（§4.1）
- 即使答案不在任何檢索文件中，NQ 上仍有 11.8% 準確率（抽取式模型為 0%）（§4.1）
- 參數量比較：T5-large（770M）NQ 28.9 EM，RAG-Sequence 44.5（附錄 G）
- Jeopardy 問題生成：人工評估 452 組，RAG 較符合事實 42.7%、BART 較符合事實 7.1%；RAG 較具體 37.4%、BART 16.8%（表 4）
- FEVER 3 類分類：與最佳 pipeline 系統差 4.3% 以內；2 類差 2.7% 以內；top-1 檢索文件來自標準答案文章 71%，top-10 含標準答案 90%（§4.4）
- 檢索消融（§4.5、表 6）：學習式檢索對所有任務有幫助；**FEVER 上 BM25 表現最好**（宣稱因 claim 以實體為主）；開放領域 QA 上可微分檢索至關重要
- 索引熱替換（index hot-swapping）：82 位世界領導人更換的測試；用 2016 索引答 2016 領導人 70%、2018 索引答 2018 領導人 68%；索引與年份不符時僅 12%、4%
- 圖 2（RAG-Token 文件後驗）：輸入「Hemingway」生成 Jeopardy 題；文件 2 提到其處女作 The Sun Also Rises（1926 年出版），文件 1 提到 A Farewell to Arms（1929）；生成「The Sun」時文件 2 後驗高、生成「A Farewell to Arms」時文件 1 高；每本書第一個 token 之後後驗趨平，作者推論標題後半可由模型參數補完（§4.3）
- 參數總量（附錄 G）：DPR 的 BERT-base 查詢與文件編碼器各 110M（文件編碼器不訓練）＋BART-large 406M，**可訓練參數共 626M**；T5-11B 為 110 億
- 檢索更多文件：RAG-Sequence 的 NQ 結果隨 K 單調提升；RAG-Token 在 K=10 達到高點（圖 3）

## 限制與觀察
- 記憶是原始文字，所以「人類可讀、可寫」，可編輯索引來更新模型（§5）
- Retrieval collapse（附錄 H）：某些任務（如故事生成）檢索器會退化為不管輸入都取同樣文件，生成器就忽略文件，效果等同 BART
- Null document 機制（附錄 F）：實驗後沒有改善，最後不採用
- Broader Impact：Wikipedia 或任何外部知識來源都不會完全符合事實且不帶偏見；也可能被濫用於產生誤導內容
- 論文中的 RAG 是「聯合微調的模型」；今日常說的 RAG（檢索＋提示）是更廣義的用法
