---
name: bilingual-pdf-translator
description: 專業金融 PDF 雙語對照翻譯。將 PDF 轉為「一段英文、一段繁體中文」格式的 TXT 檔，支援台灣金融專業術語，並採原生工具追加模式避免頻繁 Shell 授權彈窗。
---

# 雙語 PDF 翻譯技能 (Bilingual PDF Translator)

本技能旨在提供一個無縫、完整且符合專業金融語氣的 PDF 翻譯流程。它特別優化了處理大檔案的邏輯，避免觸發 Windows Shell 的頻繁授權彈窗。

## 適用場景
- 翻譯美股財報會議紀錄 (Transcripts)。
- 翻譯金融研究報告、法人說明會投影片。
- 任何需要高品質中英對照 TXT 輸出的 PDF 文件。

## 執行流程 (SOP) - 靜音指揮官 (Silent Orchestrator)

### 1. 任務啟動與術語預讀
- **全局分析**：提取公司名稱、財報年度/季度、會議日期。
- **術語全量提取**：**主 Agent 必須先讀取** [references/financial-glossary.md](references/financial-glossary.md) 與 [references/tone-guide.md](references/tone-guide.md) 的全文內容。
- **建立檔案**：寫入 CTH Banner 並留存 `[APPEND_MARKER]`。

### 2. 注入式委派 (Zero-Access Delegation)
為了避免子代理觸發 Shell/Python 授權請求，主 Agent 在調用 `invoke_agent` 時應將術語規範**直接寫入指令**中。
> **子代理指令範本：**
> 「你是專業翻譯員。任務：翻譯 PDF 第 [X-Y] 頁。
> **已讀取術語規範（請直接遵守）：**
> [在此貼入術語表全文內容]
> **執行規範：**
> - **嚴禁自行讀取路徑下的術語檔案，避免觸發 Shell 授權。**
> - **嚴禁摘要，必須逐段全文對照。**
> - 使用 `replace` 替換 `[APPEND_MARKER]`。」

### 3. 進度與回報 (Status Sync)
- 每一輪結束後，主 Agent 應向使用者廣播進度（如：`✅ 已完成第 2 頁 (11%)`）。
- **異常防範**：若子代理回報錯誤，主 Agent 應告知具體頁數。

### 4. 全文校驗與收尾
- 移除 `[APPEND_MARKER]` 前進行最終掃描。
- 提供最終路徑與統計報告。


## 注意事項
- **Session 減壓**：透過 `invoke_agent` 執行的翻譯內容不會擠爆主對話的歷史，這能維持主 Agent 的邏輯判斷力。
- **禁止偷懶**：指令必須強調「翻譯員」身份，切斷其總結或摘要的傾向。
- **原生工具**：所有子代理必須使用 `replace` 以避免彈出 Shell 授權視窗。


### 4. 自我校對與完工 (Self-Correction)
- **格式檢查**：確認發言者標註（如 Q/A）是否符合 [references/tone-guide.md](references/tone-guide.md)。
- **完整性檢查**：使用 `grep_search` 確認檔案中無遺留的長段英文或流程標記。
- 刪除最終的 `[APPEND_MARKER]`。

## 注意事項
- **禁止使用 Shell 指令**：除非 `write_file` 或 `replace` 失敗，否則嚴禁調用 `run_shell_command` 的 `Add-Content` 或是 `Out-File`。
- **OCR 修正**：讀取 PDF 時，若發現 OCR 產生的文字有明顯錯誤（如拼錯公司名），請在翻譯時主動修正。
- **金融術語**：若遇到 [references/financial-glossary.md](references/financial-glossary.md) 未收錄的術語，應優先參考台灣主要金融媒體（如鉅亨網、工商時報）的用法。
