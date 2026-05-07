/*  ================================  *\
 *                                    *
 *          C  T  H                   *
 *        created by CTH              *
 *                                    *
\*  ================================  */

# Bilingual PDF Translator (Gemini CLI Skill)

專業金融 PDF 雙語對照翻譯技能，專為 **Gemini CLI** 量身打造。

## 🌟 特色
- **金融專業化**：內建台灣金融市場常用術語庫（如：CAGR、HBM、CoWoS 等）。
- **保留關鍵英文**：自動識別並保留產業標準縮寫（如：TAM、EPS、NPU），避免過度翻譯造成的語意模糊。
- **無授權追加模式**：採用原生 `replace` 工具邏輯，翻譯長文件時不會頻繁彈出 Windows Shell 授權視窗。
- **分析師友善排版**：自動標註發言者、問答對話引用，維持高品質的 Markdown 排版。
- **本地插件支援**：支援在資料夾中放入 `CONTEXT.md` 來自定義公司專屬詞庫。

## 📂 目錄結構
- `SKILL.md`: 核心執行邏輯與 SOP。
- `references/financial-glossary.md`: 全域金融與硬體術語庫。
- `references/tone-guide.md`: 翻譯風格與排版規範。

## 🚀 安裝方式

1. **下載原始碼**：
   將此倉庫複製到您的本地目錄。

2. **打包技能**：
   在目錄外執行以下指令進行打包：
   ```bash
   node <path-to-skill-creator>/scripts/package_skill.cjs <path/to/folder>
   ```

3. **安裝至 Gemini CLI**：
   ```bash
   gemini skills install bilingual-pdf-translator.skill --scope user
   ```

4. **載入技能**：
   在對話視窗中輸入 `/skills reload`。

## 🛠️ 使用方式
直接對 Gemini CLI 說：
> 「使用翻譯技能，幫我翻譯 [PDF 路徑]」

## 📝 授權與聲明
本專案僅供研究與學術交流使用，翻譯結果僅供參考，請以原始財報文件為準。
