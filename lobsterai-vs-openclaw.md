# LobsterAI vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：LobsterAI (netease-youdao/LobsterAI), OpenClaw v2026.3.23+ | Repo: netease-youdao/LobsterAI ⭐ 4,826

---

## 1️⃣ 核心定位

| 項目 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **開發者** | 網易有道 (NetEase Youdao) — 中國頂尖科技企業 | 開源社群 |
| **設計理念** | **24/7 全場景 AI Agent** — 不僅聊天，更能執行工具完成實際工作 | 個人 AI 助理，Plugin 生態 + 外部 Agent 委派 (ACP) |
| **核心差異** | **Cowork mode（協作模式）** — Agent 不僅回應，還執行工具完成任務 | **Gateway 集中管理** — 多平台通訊 + ACP 委派外部 agent |
| **語言** | TypeScript | TypeScript (Node.js) |
| **Stars** | 4,826 ⭐ | ~57,000+ ⭐ |
| **Forks** | 716 | ~14,000+ |
| **License** | MIT | GPL-3.0 |
| **Open Issues** | 814（非常活躍，大量用戶反饋） | 高 |
| **平台支援** | macOS | Windows | Linux | Mobile — 全平台 | macOS (主要), Linux |
| **主要使用場景** | 跨平台全場景 AI 助手、Cowork 模式、網易生態整合 | 個人 AI 助理、最大通訊平台覆蓋、多 Agent 協作 |

---

## 2️⃣ 架構比較

### LobsterAI 架構

#### 全平台 Agent + Cowork Mode
- **Cowork Mode**：核心功能 — Agent **不僅聊天**，還能呼叫工具執行實際工作
- **全平台架構**：macOS + Windows + Linux + Mobile — 桌面端原生支援
- **24/7 運行**：常駐式 Agent，隨時準備執行任務
- **企業背景**：網易有道開發，具備企業級產品化能力

#### 專案結構（推斷）
| 目錄/模組 | 功能 |
|-----------|------|
| `src/agent/` | Agent 核心邏輯 |
| `src/cowork/` | Cowork 模式 — 工具呼叫與任務執行 |
| `src/platforms/` | 跨平台適配層 (macOS/Windows/Linux/Mobile) |
| `src/tools/` | 工具定義與執行框架 |
| `src/config/` | 配置管理 |
| `src/memory/` | 記憶與上下文管理 |

### OpenClaw 架構

#### Gateway + Agent Runtime 分離
- **Gateway**：WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**：Pi embedded runner (`runEmbeddedPiAgent`)
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**：channels, models, tools, skills, speech 可 plugin

### 對比總結

| 項目 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **核心架構** | 全平台 Agent + Cowork Mode | Gateway + Agent Runtime 分離 |
| **Cowork Mode** | ✅ **原生核心功能** | ⚠️ 透過 Tool 執行 (非核心定位) |
| **平台覆蓋** | ✅ macOS + Windows + Linux + Mobile | ⚠️ macOS (主要) + Linux 伺服器 |
| **Plugin 系統** | ❌ | ✅ Channels/Models/Tools/Skills 可 plugin |
| **原生 App** | ✅ 全平台桌面端 + 移動端 | ✅ macOS/iOS/Android Node |
| **Voice Wake** | ⚠️ | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |
| **Device Node Protocol** | ❌ | ✅ Camera/Screen/Location/Notification |
| **企業級支援** | ✅ 網易有道開發維護 | ⚠️ 社群驅動 |
| **部署模型** | 跨平台原生應用程式 | Node.js 服務端 + 可選前端 |

---

## 3️⃣ LLM Provider 支援

### LobsterAI
- **Provider 支援**：推斷支援主流 LLM（OpenAI、Anthropic、國內 Provider）
- **網易生態整合**：可能整合網易有道自有的 AI 服務
- **配置方式**：應用內配置或配置檔案
- **優勢**：企業級穩定性，可能有 SLA 保障

### OpenClaw
- **多 Provider**：OpenAI, Anthropic, Google, Z.ai 等多個 provider
- **Auth Profiles**：每個 agent 有自己的 `auth-profiles.json`
- **Fallback chains**：primary → fallbacks（依順序）, provider auth failover
- **Custom providers**：`models.json` 可自訂 provider
- **OAuth 支援**：OpenAI Codex subscription, Anthropic setup-token
- **Model allowlist**：`agents.defaults.models` 定義可用模型
- **Provider normalization**：自動統一 lowercase + alias

### 對比總結

| 項目 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **Provider 數量** | ⚠️ 推斷主流 + 國內 Provider | ✅ 多 (OpenAI/Claude/Google/Z.ai...) |
| **國內 Provider** | ✅ 推斷整合中國雲 AI 服務 | ⚠️ 需手動配置 |
| **動態切換** | ⚠️ | ✅ 透過 /model command |
| **Fallback chains** | ⚠️ | ✅ Primary + fallbacks |
| **Custom endpoint** | ✅ (推斷) | ✅ Custom providers in models.json |
| **OAuth 支援** | ❌ | ✅ |
| **Credential 管理** | 應用內配置 | Auth Profile + SecretRef |
| **企業級 SLA** | ✅ (網易有道) | ❌ |

---

## 4️⃣ 記憶與學習

### LobsterAI
- **Memory 系統**：推斷具備上下文管理，支援會話級記憶
- **Cowork 上下文**：協作模式下的任務上下文保持
- **跨會話記憶**：推斷支援持久化記憶

### OpenClaw
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Hybrid search**：向量 + FTS（全文搜尋）
- **Embedding provider**：可配置外部 embedding provider
- **Context compaction**：`compactEmbeddedPiSession` 自動壓縮長對話
- **Per-agent agentDir**：每個 Agent 獨立的 auth/model registry
- **Skills System**：Directory-based skills (SKILL.md + env + auto-discovery)

### 對比總結

| 項目 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **Memory 類型** | ⚠️ 推斷基礎上下文管理 | ✅ MemoryIndexManager + Markdown |
| **向量搜尋** | ❌ | ✅ Embedding + hybrid search |
| **FTS 全文搜尋** | ❌ | ✅ FTS (hybrid) |
| **Context 壓縮** | ⚠️ | ✅ compactEmbeddedPiSession |
| **Skill 系統** | ❌ | ✅ SKILL.md + 自動發現 |
| **Cowork 上下文** | ✅ 協作模式下的任務狀態保持 | ❌ (設計理念不同) |

---

## 5️⃣ 工具與擴展

### LobsterAI
- **Cowork Tools**：Cowork 模式下的工具執行框架 — **核心差異**
- **工具執行**：不僅是聊天，Agent 直接執行工具完成任務
- **擴展性**：推斷可通過 Plugin 或配置添加新工具

### OpenClaw
- **內建工具**：exec/process, code_execution, browser, web_search, read/write/edit, message, canvas, cron, image_generate
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation, group:messaging, group:nodes
- **Plugin tools**：Lobster(typed workflow), LLM Task, Diffs, OpenProse
- **CronService**：持久化 job storage, session scopes, heartbeat, webhook trigger
- **Image & Media**：openai/fal/google image gen, Whisper transcription, PDF, TTS
- **ACP & Sub-Agents**：sessions_spawn, ACPX Harness (外部 CLI 委派)

### 對比總結

| 項目 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **工具執行模式** | ✅ Cowork mode — 以工具執行為核心 | ✅ 工具執行 + 通訊為核心 |
| **Cron/排程** | ⚠️ | ✅ CronService + heartbeat |
| **圖片生成** | ❌ | ✅ openai/fal/google |
| **TTS/STT** | ❌ | ✅ OpenAI/11Labs + Whisper |
| **Sub-agents** | ❌ | ✅ ACP + ACPX |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Media Pipeline** | ❌ | ✅ audio/video/PDF |
| **Browser 自動化** | ⚠️ | ✅ |
| **跨平台工具** | ✅ macOS/Windows/Linux | ⚠️ 主要 macOS/Linux |

---

## 6️⃣ 多 Agent 架構

### LobsterAI
- **多 Agent 支援**：❌ 推斷為單一 Agent 設計，聚焦 Cowork 模式
- **設計理念**：一個強力的 AI Agent 與用戶協作，而非多個 Agent 協同
- **擴展**：可透過多實例運行

### OpenClaw
- **ACP 協議**：Agent Control Protocol，parent-child session 隔離
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Multi-agent routing**：依 channel/account/peer/guild/team 路由
- **agentToAgent**：可選工具讓 agent 互傳訊息
- **Cross-agent QMD**：可跨 Agent 搜尋記憶
- **Sub-agent registry** & **Delivery Dispatch**

### 對比總結

| 項目 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **多 Agent 支援** | ❌ | ✅ ACP 協議 + sessions_spawn/yield |
| **外部 CLI 委派** | ❌ | ✅ ACPX (Claude/Codex/Gemini/Kiro/Cursor) |
| **Agent 路由** | ❌ | ✅ 依 channel/account/peer/guild |
| **Agent 通訊** | ❌ | ✅ agentToAgent tool |
| **設計理念** | 單一強力 Agent 協作 | 多 Agent 協同體系 |
| **Sub-agent Registry** | ❌ | ✅ |
| **跨 Agent 搜尋** | ❌ | ✅ Cross-agent QMD |

---

## 7️⃣ 安全性與沙盒

### LobsterAI
- **安全模型**：企業級應用安全性
- **網易標準**：遵循企業安全標準和實踐
- **Cowork 安全**：工具執行可能具備確認/審批機制
- **跨平台安全**：需處理 macOS/Windows/Linux/Mobile 各平台的安全模型

### OpenClaw
- **Sandbox 後端**：Docker / SSH / 原生 OS 隔離
- **seccomp / AppArmor**：進程級安全策略
- **Workspace 隔離**：獨立 workspace root，per-session workspaces
- **DM Policy**：pairing / allowlist / open / disabled
- **Tool Policy**：allow/deny/profile/byProvider
- **Tool Result Guard**：驗證工具輸出形狀
- **SecretRef**：敏感資料動態解析
- **Zod Schema 驗證**：嚴格 config 驗證
- **Security Audit**：audit.ts / audit-fs.ts / fix.ts

### 對比總結

| 項目 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **核心安全模型** | 企業級標準 | 多層 (Tool Policy + Sandbox + Audit) |
| **Container Sandbox** | ⚠️ | ✅ Docker / SSH |
| **Seccomp/AppArmor** | ❌ | ✅ profiles |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Tool Result Guard** | ❌ | ✅ |
| **SecretRef** | ❌ | ✅ SecretRef + encrypted auth-profile |
| **DM Policy** | ❌ | ✅ pairing/allowlist/open/disabled |
| **Security Audit** | ❌ | ✅ audit.ts / fix.ts |
| **Zod Validation** | ❌ | ✅ |
| **企業安全標準** | ✅ 網易有道企業標準 | ⚠️ 社群標準 |
| **跨平台安全** | ✅ 處理各平台安全模型 | ⚠️ 主要伺服器端安全 |

---

## 8️⃣ 平台與 Gateway

### LobsterAI
- **平台支援**：macOS | Windows | Linux | Mobile — 真正的跨平台
- **桌面端**：原生桌面應用
- **移動端**：原生移動應用
- **通訊整合**：推斷支援主流通訊平台
- **部署**：作為應用安裝在各平台

### OpenClaw
- **通訊平台**：25+ 平台 — WhatsApp, Telegram, Slack, Discord, Signal, Matrix, Mattermost, Feishu, Google Chat, iMessage, IRC, MS Teams, LINE, Nostr, Twitch, WeChat...
- **原生 Node**：macOS / iOS / Android — 設備級 Node 協議
- **WebChat + Control UI**：內建 Web 介面
- **Gateway 控制面**：WebSocket-based Gateway

### 總結

| 項目 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **桌面平台** | ✅ macOS + Windows + Linux | ⚠️ macOS (主要) |
| **移動平台** | ✅ 原生 Mobile App | ✅ iOS/Android Node |
| **通訊平台** | ⚠️ 推斷主流 | ✅ 25+ 平台 |
| **Web Gateway** | ❌ | ✅ WebChat + Control UI (localhost:18789) |
| **Voice Wake** | ❌ (推斷) | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI |
| **Device Node Protocol** | ❌ | ✅ Camera/Screen/Location/Notification |
| **部署方式** | 安裝應用程式 | npm + 服務端部署 |

---

## 9️⃣ 架構流程圖

### A. 核心架構對比

**LobsterAI: 全平台 Cowork Agent**
```text
User Input (macOS / Windows / Linux / Mobile)
   │
   ▼
[LobsterAI Application]
   ├── Platform Adapter (OS Native)
   │
   ▼
[Cowork Mode]
   ├── Chat: 與用戶對話
   ├── Tool Call: 執行實際工具
   ├── Task Execution: 完成具體任務
   └── Respond + Continue Working
```
- 單一強大 Agent，與用戶協作完成任務
- 跨平台原生支援，統一體驗
- 24/7 常駐運行

**OpenClaw: Gateway + Agent Routing**
```text
Input (Channels) → Auth Layer(DM Policy) → Routing Engine(Bindings) → Per-Agent Runtime → LLM → Tool Policy Check → Sandbox → Respond
```
- 通訊平台為入口，Agent 為執行引擎
- 多 Agent 路由，隔離工作區

### B. 安全流程對比

**LobsterAI Security Flow**
```text
User Input → Cowork Mode → Tool Call → Execute (OS-level permissions)
                                          ↑
                              Enterprise security standards (NetEase)
```

**OpenClaw Security Flow**
```text
DM Policy → Tool Policy(allow/deny/profile) → Sandbox(Docker/seccomp/AppArmor) → SSRF → Tool Result Guard → SecretRef
```

### C. 設計理念對比

| 維度 | LobsterAI | OpenClaw |
|------|-----------|----------|
| **核心哲學** | 一個 Agent 幫你做完所有事 | 多個 Agent 協作，各司其職 |
| **交互模式** | Cowork — 協作執行 | 通訊 — Gateway 路由 |
| **擴展方式** | 跨平台原生 App | Plugin 生態系統 |
| **目標用戶** | 個人 productivity | 開發者 + power user |
| **背後力量** | 網易有道企業 | 開源社群 |

---

## 📊 總結對比表

| 維度 | LobsterAI 優勢 | OpenClaw 優勢 |
|------|----------------|---------------|
| **Cowork Mode** | ✅ **原生核心功能** — Agent 直接執行工具 | ⚠️ 透過工具執行，非核心定位 |
| **跨平台覆蓋** | ✅ macOS + Windows + Linux + Mobile | ⚠️ macOS (主要) + Linux |
| **企業級支援** | ✅ 網易有道開發維護 | ❌ 社群驅動 |
| **全場景覆蓋** | ✅ 24/7 all-scenario | ⚠️ 侧重通訊 + 開發 |
| **桌面原生體驗** | ✅ Windows + macOS + Linux | ❌ |
| **通訊平台覆蓋** | ⚠️ | ✅ 25+ 平台 |
| **多 Agent 架構** | ❌ | ✅ ACP + ACPX |
| **通訊平台整合** | ⚠️ | ✅ 最廣泛 |
| **向量記憶搜尋** | ❌ | ✅ Embedding + Hybrid |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **安全審計** | ⚠️ | ✅ audit.ts/fix.ts |
| **外部 CLI 委派** | ❌ | ✅ ACPX |
| **Voice/Media** | ❌ | ✅ TTS/STT/PDF/Image |
| **社群規模** | 4.8K ⭐ | ✅ 57K ⭐ |
| **Issue 回應** | 814 open issues (活躍) | ⚠️ |

---

## 🎯 選哪個？

| 需求 | 推薦 |
|------|------|
| **Cowork 模式 — 讓 Agent 實際執行任務** | ✅ LobsterAI (核心功能) |
| **全平台覆蓋 (macOS + Windows + Linux + Mobile)** | ✅ LobsterAI |
| **Windows 原生支援** | ✅ LobsterAI |
| **企業級產品支援** | ✅ LobsterAI (網易有道) |
| **24/7 全場景 AI 助手** | ✅ LobsterAI |
| **最大通訊平台覆蓋 (25+)** | ✅ OpenClaw |
| **多 Agent 協作架構** | ✅ OpenClaw |
| **外部 CLI 委派 (ACPX)** | ✅ OpenClaw |
| **向量長記憶 + 記憶體搜尋** | ✅ OpenClaw |
| **安全審計 + Tool Policy** | ✅ OpenClaw |
| **Plugin 生態系統** | ✅ OpenClaw |
| **原生 macOS/iOS/Android Node (Device Protocol)** | ✅ OpenClaw (Camera/Screen/Location) |
| **Workflow DSL (Lobster)** | ✅ OpenClaw |
