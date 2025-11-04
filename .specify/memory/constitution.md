<!--
Sync Impact Report

- Version change: 未定義 (template) → 1.0.0
- Modified principles: 新增並具名以下原則：
	- "MVP 優先（Minimum Viable Product）" → 聚焦最小可交付價值，避免過度設計
	- "可測性與測試優先（Testable & Test-First）" → 測試驅動與獨立可測性為非談判項
	- "簡潔與不過度設計（Simplicity & YAGNI）" → 優先簡單解法，僅在必要時擴展
	- "可觀察性（Observability）" → 結構化日誌、錯誤追蹤與可量測指標
	- "版本語義與破壞性變更（SemVer & Breaking Changes）" → 明確版本政策與遷移流程
- Added sections: Development Workflow (開發流程)、Governance（治理）—具體化修憲程序與版本規則
- Removed sections: 無（原模板為占位，改為具體化內容）
- Templates requiring updates:
	- /Users/mei/Documents/未命名2/IdeaBox/.specify/templates/plan-template.md ✅ updated
	- /Users/mei/Documents/未命名2/IdeaBox/.specify/templates/spec-template.md ✅ updated
	- /Users/mei/Documents/未命名2/IdeaBox/.specify/templates/tasks-template.md ⚠ pending
	- /Users/mei/Documents/未命名2/IdeaBox/.specify/templates/checklist-template.md ⚠ pending
	- /Users/mei/Documents/未命名2/IdeaBox/.specify/templates/agent-file-template.md ⚠ pending

- Follow-up TODOs:
	- TODO(RATIFICATION_DATE): 原始採納日期不明，請補入採納日期或保留 TODO 條目
	- 檢查並在 `/specify/templates/tasks-template.md`, `checklist-template.md`, `agent-file-template.md` 中加入或確認「憲法合規」檢查點（待手動確認或自動化）

-- End Sync Impact Report
-->

# IdeaBox Constitution

## Core Principles

### 一、MVP 優先（Minimum Viable Product）
產品開發必須以最小可行產品為導向。每個里程碑應該交付可演示、可測試的使用者價值。
規則：MUST 以最少的工作量交付可驗證的價值；任何額外設計或架構擴展都必須在明確的業務需求和驗證後才被允許。
理由：減少浪費，快速獲得使用者回饋並降低技術債。

### 二、可測性與測試優先（Testable & Test-First）
所有重要行為必須可被自動化測試覆蓋。對於每個使用者故事，必須至少定義一個可獨立執行的驗收測試（Acceptance Test）。對於核心邏輯與邊界條件，優先採用單元測試；對跨模組流程採用整合測試或 UI 測試。
規則：MUST 將測試視為交付標準之一；在實作前撰寫測試（Test-First）或至少為每個故事提供明確的驗收案例。
理由：可測性提供回歸保護並讓重構更安全。

### 三、簡潔與不過度設計（Simplicity & YAGNI）
系統應當優先採用簡單、直接且可理解的方案。避免提前最佳化或設計大範圍抽象，除非有明確且可驗證的需求驅動。
規則：SHOULD 保持實作簡潔；任何增加的複雜度必須在 PR 中以「必要性說明」列出並經審查批准。
理由：降低維護成本與錯誤率，同時保持開發速度。

### 四、可觀察性（Observability）
系統必須提供足夠且結構化的可觀察性資料以便調試與衡量：包含錯誤追蹤、結構化日誌（可輸出為 JSON 或相容格式）、基本指標（例如請求成功率、延遲 p95）與必要的事件紀錄。
規則：SHOULD 設定適當的日誌等級與格式；MUST 在關鍵流程中記錄足夠上下文以支持事後分析。
理由：快速定位問題並衡量系統健康度。

### 五、版本語義與破壞性變更（SemVer & Breaking Changes）
採用語義版本控制：MAJOR.MINOR.PATCH。任何破壞性 API 或資料結構變更，必須升級 MAJOR，並提供明確遷移指南與兼容策略。
規則：MUST 在 PR 或發行說明中包含版本影響評估；破壞性變更須附帶遷移計畫與回退策略。
理由：保護使用者與下游系統，降低升級風險。

## 開發約束與技術範圍
本專案主要技術棧：Swift + SwiftUI（iOS）、XCTest 作為測試框架；若需使用持久化，優先考慮 CoreData 或輕量檔案存取。任何新增重大技術選型需在 plan.md 中列出並說明替代方案評估。

## 開發流程（Development Workflow）
- 所有變更經由 Pull Request（PR）進行，PR 必須：
	- 附上「變更描述」與必要的驗證步驟
	- 指定對應的測試或驗收案例
	- 指出是否影響版本（MAJOR/MINOR/PATCH）與破壞性說明
- 代碼審查（至少一位 reviewer）與 CI 綠燈為合併前提
- 門檻（Quality Gates）：PR 必須通過單元測試與靜態分析（若適用），並在 tasks/spec/plan 檔案中標註憲法檢查（Constitution Compliance）完成狀態

## Governance（治理）
- 憲法優先於一般指引：當衝突發生時，以本憲法為準；例外必須以書面方式提出並記錄在相應的 plan 或 spec 中，說明理由與替代方案。
- 修憲程序：
	1. 提交 PR 修改 `.specify/memory/constitution.md`，包含變更說明與影響評估（含所需更新的模板與運行時文件）。
	2. 至少一位核心維護者（或授權審核者）審查並接受；若為破壞性治理變更或新增原則，需在 PR 描述中標註為 MAJOR 提案並取得社群或維護群的明確同意。
	3. 修訂合併後，`Last Amended` 更新為合併日期；若該變更影響運行時或模板，應在 7 個工作日內完成相關模板與自動化檢查更新或註明待辦。
- 版本規則：
	- MAJOR：對治理原則、原則移除或與向後不相容的重大重定義
	- MINOR：新增原則或對現有原則進行實質擴展
	- PATCH：文字、措辭、排版修正或小幅澄清

## 合規檢查（Compliance & Validation）
- 在合併任何 feature 或 spec 前，請在對應的 plan.md/spec.md/tasks.md 中加入「Constitution Compliance」章節，說明如何滿足本憲法的相關條款。
- 自動化：CI 應檢查 PR 是否包含對應的測試與版本影響評估（可逐步推動，初期為手動審查）。

## Governance Contact
若需例外、釐清或修憲討論，請聯絡專案維護者或在專案溝通頻道提出議題並附上參考 PR。

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): 未知，請補入採納日期 | **Last Amended**: 2025-11-04
