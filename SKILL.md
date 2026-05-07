---
name: bilingual-pdf-translator
description: 專業金融 PDF 雙語對照翻譯技能 (Silent Orchestrator 模式)。採用自動路徑鎖定與子代理注入邏輯，確保翻译完整且無彈窗干擾。
---

# 雙語 PDF 翻譯技能 (Bilingual PDF Translator)

本技能採用進階「指揮官模式」，主 Agent 負責系統級路徑定錨與任務協調，具體翻譯任務由子代理 (`generalist`) 執行，以確保翻譯高品質並維持主 Session 輕量化。

## 核心原則
- **自動路徑定錨 (Deterministic Pathing)**：嚴禁 AI 憑直覺拼湊路徑。必須向作業系統獲取 PDF 的真實絕對路徑，確保存檔位置 100% 準確且無需使用者確認。
- **資訊注入 (Zero-Access Delegation)**：主 Agent 將術語規範直接寫入子代理指令，禁止子代理自行存取全域檔案，從根本上消滅 Python/Shell 授權彈窗。
- **嚴禁摘要 (No Summary)**：主體任務為「全文逐段對照翻譯」。嚴禁以總結取代譯文，必須確保原文每一段都有對應中文。

## 執行流程 (SOP)

### Phase 1: 系統級路徑定錨 (Pre-flight)
1. **獲取絕對路徑**：主 Agent 對 PDF 執行 `list_directory` 或系統指令，獲取其完整的 **Full Path**。
2. **定義唯一目標**：將 Full Path 的 `.pdf` 替換為 `_translated.txt`，定義為唯一的 **[TARGET_PATH]**。
3. **初始化檔案**：直接在 **[TARGET_PATH]** 建立檔案，寫入 CTH Banner 與 Metadata（公司、季度、日期），並留存 `[APPEND_MARKER]`。

### Phase 2: 注入式子代理委派 (Silent Delegation)
1. **規則預讀**：主 Agent 讀取 `financial-glossary.md` 與 `tone-guide.md`。
2. **指派任務**：以 **2 頁** 為單位循環，調用 `invoke_agent` 傳遞以下注入式指令：
   > **子代理指令範本：**
   > 「你是專業翻譯機器人。任務：翻譯 PDF 第 [X-Y] 頁。
   > **絕對存檔路徑：[TARGET_PATH]**
   > **已注入規則（請直接遵守，禁止讀取外部檔）：**
   > - 術語：[在此貼入術語表精華，特別是保留英文部分]
   > - 格式：英文段落 \n 繁體中文譯文。
   > - **嚴禁摘要，必須全文翻譯。**
   > - 使用 `replace` 替換 `[APPEND_MARKER]` 為『譯文 + [APPEND_MARKER]』。」

### Phase 3: 透明進度與最終校驗
1. **進度廣播**：每一輪結束，主 Agent 向使用者報告：`✅ 已完成第 [N/M] 頁 ([百分比])`。
2. **完整性掃描**：翻譯結束後，主 Agent 掃描 **[TARGET_PATH]**，確認無遺漏段落或流程標記。
3. **標記清理**：移除最後的 `[APPEND_MARKER]` 並提供最終檔案連結。

## 注意事項
- **禁止使用 Shell 指令**：除非原生工具失敗，否則嚴禁調用 `Add-Content` 等指令。
- **跨公司通用**：若目錄下有 `CONTEXT.md`，主 Agent 應優先讀取並將其內容一同注入給子代理。
