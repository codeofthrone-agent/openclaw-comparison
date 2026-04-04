# NullClaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：NullClaw (nullclaw/nullclaw), OpenClaw v2026.3.23+ | Repo: nullclaw/nullclaw ⭐ 7,024

---

## 1️⃣ 核心定位

| 項目 | NullClaw | OpenClaw |
|------|----------|----------|
| **開發者** | nullclaw (社群) | 開源社群 |
| **設計理念** | **極致速度與最小體積** — 用 Zig 編寫的最快、最小的全自主 AI 助理 | 個人 AI 助理，Plugin 生態 + 外部 Agent 委派 (ACP) |
| **核心差異** | **~678 KB 二進制、<2ms 啟動、~1MB 峰值 RSS** — 嵌入式極簡主義 | **全功能平台** — Gateway 架構、25+ 通訊平台、原生 App |
| **語言** | Zig | TypeScript (Node.js) |
| **Stars** | 7,024 ⭐ | ~57,000+ ⭐ |
| **Forks** | 833 | ~14,000+ |
| **License** | MIT | GPL-3.0 |
| **Open Issues** | 108 | 高 |
| **關聯專案** | nullhub (UI 安裝配置介面，beta) | 無 |
| **主要使用場景** | 嵌入式裝置、資源受限環境、CI/CD pipeline、需要極快啟動速度的場景 | 個人 AI 助理、最大通訊平台覆蓋、多 Agent 協作、預測市場整合 |

---

## 2️⃣ 架構比較

### NullClaw 架構

#### 極簡單體架構
- **二進制大小**：~678 KB（編譯後單一靜態二進制）
- **啟動時間**：<2ms — 幾乎即時啟動
- **記憶體佔用**：峰值 ~1MB RSS — 極致節省
- **語言**：Zig — 零运行时、記憶體安全、高性能
- **部署模型**：單一靜態二進制檔案，無依賴
- **關聯 UI**：nullhub（Web UI 安裝配置介面，beta 階段）

#### 專案結構（推斷）
| 目錄/模組 | 功能 |
|-----------|------|
| `src/main.zig` | 主入口 |
| `src/agent/` | Agent 核心邏輯 |
| `src/llm/` | LLM API 客戶端 |
| `src/platforms/` | 通訊平台適配器 (19 個) |
| `src/tools/` | 工具定義與執行 |
| `src/memory/` | 記憶管理 |

### OpenClaw 架構

#### Gateway + Agent Runtime 分離
- **Gateway**：WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**：Pi embedded runner (`runEmbeddedPiAgent`)
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**：channels, models, tools, skills, speech 可 plugin

### 對比總結

| 項目 | NullClaw | OpenClaw |
|------|----------|----------|
| **核心架構** | 極簡單體 + 單一靜態二進制 | Gateway + Agent Runtime 分離 |
| **二進制大小** | ✅ ~678 KB | ❌ Node.js + npm (百 MB 級) |
| **啟動時間** | ✅ <2ms | ❌ 數百 ms 至數秒 |
| **記憶體佔用** | ✅ ~1MB RSS | ❌ 數十至數百 MB |
| **語言** | Zig（編譯型、無 runtime） | TypeScript/Node.js |
| **依賴** | ✅ 零外部依賴 | ❌ npm + Node.js runtime |
| **Plugin 系統** | ❌ 無 | ✅ Channels/Models/Tools/Skills 可 plugin |
| **關聯 UI** | nullhub (beta) | ✅ WebChat + Control UI |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |

---

## 3️⃣ LLM Provider 支援

### NullClaw
- **Provider 支援**：推斷支援主流 LLM API（OpenAI-compatible endpoints）
- **配置方式**：可能透過配置檔案或環境變數設定 API endpoint
- **優勢**：Zig 的高效能 HTTP 客戶端，API 呼叫延遲極低
- **限制**：作為極簡專案，可能僅支援基礎的 API 呼叫模式

### OpenClaw
- **多 Provider**：OpenAI, Anthropic, Google, Z.ai 等多個 provider
- **Auth Profiles**：每個 agent 都有自己的 `auth-profiles.json`
- **Fallback chains**：primary → fallbacks（依順序）
- **Custom providers**：`models.json` 可自訂 provider
- **OAuth 支援**：OpenAI Codex subscription, Anthropic setup-token
- **Model allowlist**：`agents.defaults.models` 定義可用模型
- **Provider normalization**：自動統一 lowercase + alias

### 對比總結

| 項目 | NullClaw | OpenClaw |
|------|----------|----------|
| **Provider 數量** | ⚠️ 基礎（推斷僅主流 API） | ✅ 多 (OpenAI/Claude/Google/Z.ai...) |
| **動態切換** | ⚠️ 可能需重啟 | ✅ 透過 /model command |
| **Fallback chains** | ❌ | ✅ Primary + fallbacks |
| **Custom endpoint** | ✅ (推斷) | ✅ Custom providers in models.json |
| **Model allowlist** | ❌ | ✅ agents.defaults.models |
| **OAuth 支援** | ❌ | ✅ |
| **Credential 管理** | 基礎 | Auth Profile per-agent + SecretRef |
| **API 效能** | ✅ Zig 原生 HTTP，極低延遲 | ❌ Node.js HTTP |

---

## 4️⃣ 記憶與學習

### NullClaw
- **Memory 系統**：推斷使用極簡的 Markdown/JSON 檔案儲存
- **輕量設計**：受限於 ~1MB RSS，記憶系統必為極簡實現
- **Session 管理**：可能基於檔案系統的會話記錄

### OpenClaw
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Hybrid search**：向量 + FTS（全文搜尋）
- **Embedding provider**：可配置外部 embedding provider
- **Context compaction**：`compactEmbeddedPiSession` 自動壓縮長對話
- **Per-agent agentDir**：每個 Agent 獨立的 auth/model registry
- **Skills System**：Directory-based skills with env injection + auto-discovery

### 對比總結

| 項目 | NullClaw | OpenClaw |
|------|----------|----------|
| **Memory 類型** | ⚠️ 推斷基礎檔案儲存 | ✅ MemoryIndexManager + Markdown |
| **向量搜尋** | ❌ | ✅ Embedding + hybrid search |
| **FTS 全文搜尋** | ❌ | ✅ FTS (hybrid) |
| **Context 壓縮** | ❌ | ✅ compactEmbeddedPiSession |
| **Skill 系統** | ❌ | ✅ SKILL.md + 自動發現 |
| **每輪記憶注入** | ❌ | ✅ (透過 Bootstrap Files) |

---

## 5️⃣ 工具與擴展

### NullClaw
- **工具系統**：推斷支援基本工具呼叫（terminal、file、web search）
- **Zig 執行**：無外部依賴，工具執行效率極高
- **限制**：受限於極簡設計，工具類型可能較少

### OpenClaw
- **內建工具**：exec/process, code_execution, browser, web_search, read/write/edit, message, canvas, cron, image_generate
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation, group:messaging, group:nodes
- **Plugin tools**：Lobster(typed workflow), LLM Task, Diffs, OpenProse
- **CronService**：持久化 job storage, session scopes, heartbeat, webhook trigger
- **Image & Media**：image_generate(openai/fal/google), Whisper transcription, PDF, TTS
- **ACP & Sub-Agents**：sessions_spawn, ACPX Harness

### 對比總結

| 項目 | NullClaw | OpenClaw |
|------|----------|----------|
| **內建工具** | ⚠️ 基礎工具（推斷） | ✅ 12+ 內建 + plugins |
| **Cron/排程** | ❌ | ✅ CronService + heartbeat |
| **圖片生成** | ❌ | ✅ openai/fal/google |
| **TTS/STT** | ❌ | ✅ OpenAI/11Labs + Whisper |
| **Sub-agents** | ❌ | ✅ ACP + ACPX |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Media Pipeline** | ❌ | ✅ audio/video/PDF |
| **Browser 自動化** | ❌ | ✅ |
| **工具執行效能** | ✅ Zig 原生，極快 | ⚠️ Node.js |

---

## 6️⃣ 多 Agent 架構

### NullClaw
- **多 Agent 支援**：❌ 推斷為單一 Agent 設計
- **隔離**：作為單一二進制程式，原生不支持多 Agent 隔離
- **擴展性**：可透過啟動多個實例實現，但需手動管理

### OpenClaw
- **ACP 協議**：Agent Control Protocol，parent-child session 隔離
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Multi-agent routing**：依 channel/account/peer/guild/team 路由
- **agentToAgent**：可選工具讓 agent 互傳訊息
- **Cross-agent QMD**：可跨 Agent 搜尋記憶
- **Sub-agent registry** & **Delivery Dispatch**

### 對比總結

| 項目 | NullClaw | OpenClaw |
|------|----------|----------|
| **多 Agent 支援** | ❌ | ✅ ACP 協議 + sessions_spawn/yield |
| **外部 CLI 委派** | ❌ | ✅ ACPX (Claude/Codex/Gemini/Kiro/Cursor) |
| **Agent 路由** | ❌ | ✅ 依 channel/account/peer/guild |
| **Agent 通訊** | ❌ | ✅ agentToAgent tool |
| **跨 Agent 搜尋** | ❌ | ✅ Cross-agent QMD |
| **Sub-agent Registry** | ❌ | ✅ |
| **遞迴保護** | ✅ (單 Agent 天生免疫) | ✅ ACP 框架控制 |

---

## 7️⃣ 安全性與沙盒

### NullClaw
- **安全模型**：Zig 記憶體安全特性（編譯時間邊界檢查）
- **沙盒**：無內建沙盒，依賴 OS 級隔離
- **權限**：極小攻擊面（678 KB 二進制）
- **風險**：無額外安全層，需依賴外部 sandbox

### OpenClaw
- **Sandbox 後端**：Docker / SSH / 原生 OS 隔離
- **seccomp / AppArmor**：進程級安全策略
- **Workspace 隔離**：獨立 workspace root，per-session workspaces
- **DM Policy**：pairing / allowlist / open / disabled
- **Tool Policy**：allow/deny/profile/byProvider
- **Tool Result Guard**：驗證工具輸出形狀
- **SecretRef**：敏感資料動態解析
- **Zod Schema 驗證**：嚴格 config 驗證，拒絕未知 key
- **Security Audit**：audit.ts / audit-fs.ts / fix.ts

### 對比總結

| 項目 | NullClaw | OpenClaw |
|------|----------|----------|
| **核心安全模型** | Zig 記憶體安全（編譯期） | 多層 (Tool Policy + Sandbox + Audit) |
| **Container Sandbox** | ❌ | ✅ Docker / SSH |
| **Seccomp/AppArmor** | ❌ | ✅ profiles |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Tool Result Guard** | ❌ | ✅ 驗證輸出形狀 |
| **SecretRef** | ❌ | ✅ SecretRef + encrypted auth-profile |
| **DM Policy** | ❌ | ✅ pairing/allowlist/open/disabled |
| **Security Audit** | ❌ | ✅ audit.ts / fix.ts |
| **Zod Validation** | ❌ | ✅ 拒絕未知 key |
| **攻擊面** | ✅ 極小（678 KB 二進制） | ⚠️ 較大（完整 Node.js 應用） |

---

## 8️⃣ 平台與 Gateway

### NullClaw
支援 19 個通訊平台（經 README 確認）：
- **涵蓋範圍**：Discord, Telegram, Slack, WhatsApp, Signal, Matrix, 及其他 13 個平台
- **實現方式**：Zig 原生 HTTP/WebSocket 客戶端
- **部署**：單一靜態二進制直接執行

### OpenClaw
支援 25+ 個通訊平台（含原生 App）：
- WhatsApp, Telegram, Slack, Discord, Signal, Matrix, Mattermost, Feishu, Google Chat, iMessage, IRC, MS Teams, LINE, Nostr, Twitch, WeChat, macOS/iOS/Android App, WebChat...

### 總結

| 項目 | NullClaw | OpenClaw |
|------|----------|----------|
| **平台數量** | 19 | ~25+ |
| **共有平台** | Discord, Telegram, Slack, WhatsApp, Signal, Matrix 等 | 同左 + 更多 |
| **NullClaw 可能獨有** | 取決於 19 個平台的具體列表 | — |
| **OpenClaw 獨有** | — | Google Chat, iMessage, Teams, LINE, Nostr, Twitch, WeChat, macOS/iOS/Android |
| **原生 App** | ❌ | ✅ macOS / iOS / Android |
| **Web UI** | nullhub (beta) | ✅ WebChat + Control UI |
| **Voice Wake** | ❌ | ✅ |
| **部署複雜度** | ✅ 單一執行檔 | ⚠️ npm install + 配置 |

---

## 9️⃣ 架構流程圖

### A. 核心架構對比

**NullClaw: 極簡靜態二進制**
```text
User Input (19 platforms)
   │
   ▼
[Single Binary: ~678KB]
   ├── Platform Adapter (Zig native)
   │
   ▼
[Agent Loop]
   ├── LLM API Call (Zig HTTP client)
   ├── Tool Dispatch
   └── Respond
```
- 單一進程、單一線程（或少數線程）
- 無垃圾回收、無 runtime 開銷
- 啟動 <2ms，峰值記憶體 ~1MB

**OpenClaw: Gateway + Agent Routing**
```text
Input (Channels) → Auth Layer(DM Policy) → Routing Engine(Bindings) → Per-Agent Runtime → LLM → Tool Policy Check → Sandbox → Respond
```

### B. 安全流程對比

**NullClaw Security Flow**
```text
Input → Zig binary → LLM call → Tool exec → Respond
                       ↑
              Minimal attack surface (678KB)
              OS-level sandboxing only
```

**OpenClaw Security Flow**
```text
DM Policy → Tool Policy(allow/deny/profile) → Sandbox(Docker/seccomp/AppArmor) → SSRF → Tool Result Guard → SecretRef
```

### C. 資源使用對比

| 指標 | NullClaw | OpenClaw |
|------|----------|----------|
| **二進制大小** | ~678 KB | ~100+ MB (Node.js + deps) |
| **啟動時間** | <2ms | 500ms - 3s |
| **峰值 RSS** | ~1MB | ~100-300 MB |
| **記憶體安全** | ✅ Zig 編譯期檢查 | ✅ TypeScript (但 runtime 有風險) |
| **部署** | scp 即可 | npm install + 配置 |

---

## 📊 總結對比表

| 維度 | NullClaw 優勢 | OpenClaw 優勢 |
|------|---------------|---------------|
| **極速啟動** | ✅ <2ms 啟動 | ❌ 數百 ms 至數秒 |
| **極小體積** | ✅ ~678 KB 二進制 | ❌ Node.js 生態巨大 |
| **記憶體佔用** | ✅ ~1MB 峰值 RSS | ❌ 100-300 MB |
| **零依賴部署** | ✅ 單一靜態二進制 | ❌ npm + Node.js |
| **嵌入式可用** | ✅ 適合嵌入式/邊緣計算 | ❌ |
| **通訊平台** | 19 個平台 | ✅ 25+ 平台 |
| **多 Agent 支援** | ❌ | ✅ ACP + ACPX |
| **LLM 靈活性** | ⚠️ 基礎 | ✅ 多 provider + fallback |
| **記憶能力** | ❌ 基礎 | ✅ 向量搜尋 + Hybrid |
| **工具生態** | ⚠️ 基礎 | ✅ 12+ 內建 + plugins |
| **安全性** | ⚠️ 依賴 OS | ✅ 多層安全架構 |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ |
| **社群規模** | ⚠️ 7K ⭐ | ✅ 57K ⭐ |

---

## 🎯 選哪個？

| 需求 | 推薦 |
|------|------|
| **嵌入式/物聯網裝置部署** | ✅ NullClaw (678 KB, 1MB RAM) |
| **CI/CD pipeline AI assistant** | ✅ NullClaw (<2ms 啟動) |
| **極端資源受限環境** | ✅ NullClaw (~1MB RSS) |
| **單一執行檔部署的便利** | ✅ NullClaw (scp 即可) |
| **多平台通訊覆蓋 (25+)** | ✅ OpenClaw |
| **多 Agent 協作架構** | ✅ OpenClaw |
| **原生 macOS/iOS/Android App** | ✅ OpenClaw |
| **向量長記憶 + 向量搜尋** | ✅ OpenClaw |
| **外部 CLI 委派 (ACPX)** | ✅ OpenClaw |
| **安全審計 + Tool Policy** | ✅ OpenClaw |
| **Workflow DSL (Lobster)** | ✅ OpenClaw |
| **邊緣計算 / Serverless cold start** | ✅ NullClaw |
