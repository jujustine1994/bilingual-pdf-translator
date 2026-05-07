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

## 執行流程 (SOP)

### 1. 預掃描與實體提取 (Pre-scan & Entity Extraction)
- 在正式翻譯前，先讀取 PDF 全文內容。
- **提取關鍵實體**：列出文中出現的人名、產品代碼、專有名詞（如特定處理器型號）。
- **核對詞庫**：根據 [references/financial-glossary.md](references/financial-glossary.md) 確定譯名與保留英文的術語。

### 2. 初始化目標檔案
- 建立 `.txt` 檔案，包含 CTH Banner。
- 寫入第一段翻譯並附上 `[APPEND_MARKER]`。

### 3. 流式分塊讀取與翻譯 (Chunked Processing)
為確保高品質與穩定性：
- 每次處理 **2-3 頁** 的內容。
- 逐段翻譯並使用 `replace` 原生工具將 `[APPEND_MARKER]` 替換為「新譯文 + `[APPEND_MARKER]`」。
- **數據檢驗**：每完成一頁翻譯，需對比原文數字，確保無任何數據偏移。

### 4. 自我校對與完工 (Self-Correction)
- **格式檢查**：確認發言者標註（如 Q/A）是否符合 [references/tone-guide.md](references/tone-guide.md)。
- **完整性檢查**：使用 `grep_search` 確認檔案中無遺留的長段英文或流程標記。
- 刪除最終的 `[APPEND_MARKER]`。

## 注意事項
- **禁止使用 Shell 指令**：除非 `write_file` 或 `replace` 失敗，否則嚴禁調用 `run_shell_command` 的 `Add-Content` 或是 `Out-File`。
- **OCR 修正**：讀取 PDF 時，若發現 OCR 產生的文字有明顯錯誤（如拼錯公司名），請在翻譯時主動修正。
- **金融術語**：若遇到 [references/financial-glossary.md](references/financial-glossary.md) 未收錄的術語，應優先參考台灣主要金融媒體（如鉅亨網、工商時報）的用法。
