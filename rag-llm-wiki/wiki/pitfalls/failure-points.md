# RAG 的七個失敗點（Barnett et al. 2024）

| # | 名稱 | 發生什麼事 | 出在流程哪一段 |
|---|---|---|---|
| FP1 | Missing Content | 問題無法由現有文件回答；系統可能被「相關但無答案」的問題騙出一個回答 | 索引內容 |
| FP2 | Missed the Top Ranked Documents | 答案在文件裡，但排名不夠前沒被回傳（只回傳 top K） | 檢索、重排序 |
| FP3 | Not in Context — Consolidation strategy limitations | 文件被檢索到，但整併時沒進入生成用 context | Consolidator |
| FP4 | Not Extracted | 答案在 context 中，LLM 沒抽出；常因雜訊或互相矛盾的內容 | 生成 / Reader |
| FP5 | Wrong Format | 要求表格或清單等格式，LLM 忽略指令 | 生成 / Reader |
| FP6 | Incorrect Specificity | 答案太籠統或太細；也發生在使用者不知怎麼問時 | 生成、問題 |
| FP7 | Incomplete | 答案沒錯但漏掉 context 中有的資訊；例：問「A、B、C 的重點」，宜分開問 | 生成 |

來源：raw/04 §5
