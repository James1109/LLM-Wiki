# 04 Barnett et al. 2024 — Seven Failure Points When Engineering a RAG System
- 來源：https://arxiv.org/pdf/2401.05856 （CAIN 2024，Lisbon；arXiv v1 2024-01-11）；作者來自 Deakin University
- 讀取日期：2026-11-10｜**已讀範圍：全文**｜經驗報告（experience report），非對照實驗

## 設定
- 三個案例：Cognitive Reviewer（研究，使用者上傳論文）、AI Tutor（教育，整合學習管理系統，影片先用 Whisper 轉錄；2023 年 10 月底開始在 200 名學生的課程試行）、BioASQ（生醫，1000 題）
- RAG 兩個流程：Index（開發時）與 Query（執行時）。Query 包含：query rewriter（用聊天紀錄改寫模糊問題，如「再多解釋一點」）→ 檢索（top-k，如 cosine 相似度）→ re-rank → Consolidator（因 token 與速率限制整併 chunk）→ Reader（過濾雜訊、遵循格式、產出答案）
- 設計抉擇：chunk 太小有些問題答不了，太長則答案含生成雜訊；**更換 embedding 策略需重新索引所有 chunk**
- BioASQ 評估：用 OpenAI evals 自動判斷，再人工檢查 40 個問題與所有被標為不正確者；自動評估比人類評分者「更悲觀」；作者承認人工檢查者不是領域專家，有效度威脅
- ⚠️ 文件數量不一致：§1 寫 15,000 份文件，§4.3 寫下載 4,017 份；**簡報不引用文件數**

## 七個失敗點（§5，原文）
- FP1 Missing Content：問題無法由現有文件回答。理想是回「抱歉我不知道」，但與內容相關卻沒有答案的問題，系統可能被騙而給出回答
- FP2 Missed the Top Ranked Documents：答案在文件中，但排名不夠前沒被回傳（實務上只回傳 top K）
- FP3 Not in Context — Consolidation strategy limitations：含答案的文件被檢索到，但沒進入生成用的 context（整併時被丟掉）
- FP4 Not Extracted：答案在 context 裡，但 LLM 沒抽出正確答案；常因雜訊太多或內容互相矛盾
- FP5 Wrong Format：要求特定格式（表格、清單），LLM 忽略指令
- FP6 Incorrect Specificity：答案不夠具體或過度具體；也發生在使用者不知怎麼問、問得太籠統時
- FP7 Incomplete：答案不錯但漏掉 context 中有的資訊。例「文件 A、B、C 的重點是什麼？」，較好做法是分開問

## 經驗（§6、表 2）
- 更大的 context 結果較好（AI Tutor：8K 優於 4K）（FP4）
- Semantic cache 可降低成本與延遲（FP1）
- 在檢索內容加入檔名與 chunk 編號等 metadata 有助檢索與抽取（FP2、FP4）
- 開源 embedding 模型在短文本上表現與閉源相當
- **RAG 系統需要持續校準**：輸入在執行時才知道，需不斷監控（FP2–7）
- 要校準的項目：chunk 大小、embedding 策略、chunking 策略、檢索策略、consolidation 策略、context 大小、prompt
- **效能測試只能在執行時進行**；離線評估如 G-Eval 需要有標註的問答資料
- 越獄（jailbreak）可繞過 RAG 系統直達安全訓練；微調可能逆轉安全訓練，微調過的 LLM 都要測
- 兩個核心結論：(1) 驗證只能在運作中進行；(2) 穩健性是演化出來的，不是一開始設計好

## 論文對 RAG vs 微調的看法（§6.2）
- 微調：資料烘進模型；基礎模型演進或有新資料要重新微調；需處理安全與隱私
- RAG：可依需求切 chunk，只把相關 chunk 放進 context；可持續更新知識，並控制使用者能存取哪些 chunk；但最佳 chunk embedding、檢索與融合策略仍在研究中
