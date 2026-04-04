# NanoClaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：NanoClaw (qwibitai/nanoclaw), OpenClaw v2026.3.23+ | Repo: qwibitai/nanoclaw ⭐ 26,373

---

## 1️⃣ 核心定位

| 項目 | NanoClaw | OpenClaw |
|------|----------|----------|
| **開發者** | qwibitai (社群) | 開源社群 |
| **設計理念** | 每個 Agent 運行在隔離容器中，基於 Anthropic Agents SDK 構建 | 個人 AI 助理，Plugin 生態 + 外部 Agent 委派 (ACP) |
| **核心差異** | **安全性內建於隔離架構** — 每個 Agent = 獨立容器，天然沙盒 | **Gateway 集中管理** — 單一 Gateway 路由多個 Agent，Sandbox 需額外配置 |
| **語言** | TypeScript | TypeScript (Node.js) |
| **Stars** | 26,373 ⭐ | ~57,000+ ⭐ |
| **Fork 率** | 39% (10,390 forks) — 極高社群參與度 | ~25% |
| **License** | MIT | GPL-3.0 |
| **主要使用場景** | 多 Agent 協作、需要嚴格隔離的企業/開發場景、Scheduled Jobs | 個人 AI 助理、最大通訊平台覆蓋、原生 App 生態 |

---

## 2️⃣ 架構比較

### NanoClaw 架構

#### 容器隔離架構
- **隔離模型**：每個 Agent 運行在獨立 Docker 容器中
- **SDK 基礎**：Anthropic Agents SDK，原生支援 tool-calling 和 conversation management
- **通訊層**：Gateway 負責 Agent 間的消息路由和容器間通訊
- **部署**：Docker Compose / Kubernetes-ready

#### 專案結構（推斷）
| 目錄/模組 | 功能 |
|-----------|------|
| `agents/` | Agent 定義和配置 |
| `containers/` | Docker 容器定義和啟動邏輯 |
| `gateway/` | 消息路由和容器間通訊 |
| `memory/` | Agent 持久化記憶 |
| `scheduler/` | Scheduled Jobs 管理 |

### OpenClaw 架構

#### Gateway + Agent Runtime
- **Gateway**：WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**：Pi embedded runner (`runEmbeddedPiAgent`)
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**：channels, models, tools, skills, speech 可 plugin

### 對比總結

| 項目 | NanoClaw | OpenClaw |
|------|----------|----------|
| **核心架構** | 容器隔離 (每 Agent = 容器) | Gateway + Agent Runtime 分離 |
| **隔離粒度** | ✅ 容器級 (Docker) | ⚠️ 進程級 (需配置 Docker sandbox) |
| **SDK 基礎** | Anthropic Agents SDK | Pi embedded runner (自研) |
| **Plugin 系統** | ❌ Agent 為核心單元 | ✅ Channels/Models/Tools/Skills 可 plugin |
| **部署模型** | Docker Compose / K8s | Node.js + 可選 Docker sandbox |
| **輔助模型** | ❌ | ❌ |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |

---

## 3️⃣ LLM Provider 支援

### NanoClaw
- **Anthropic 生態**：基於 Anthropic Agents SDK，原生支援 Claude 系列模型
- **Provider 鎖定**：主要與 Anthropic 生態綁定
- **API Key 管理**：透過容器環境變數或配置檔案注入

### OpenClaw
- **多 Provider**：OpenAI, Anthropic, Google, Z.ai 等多個 provider
- **Auth Profiles**：每個 agent 有自己的 `auth-profiles.json`
- **Fallback chains**：primary → fallbacks（依順序）
- **Custom providers**：`models.json` 可自訂 provider
- **OAuth 支援**：OpenAI Codex, Anthropic setup-token
- **Model allowlist**：`agents.defaults.models` 定義可用模型

### 對比總結

| 項目 | NanoClaw | OpenClaw |
|------|----------|----------|
| **Provider 數量** | 1 (Anthropic/Claude) | 多 (OpenAI/Claude/Google/Z.ai...) |
| **動態切換** | ❌ | ✅ 透過 /model command |
| **Fallback chains** | ❌ | ✅ Primary + fallbacks |
| **Custom endpoint** | ❌ | ✅ Custom providers in models.json |
| **Model allowlist** | ❌ | ✅ agents.defaults.models |
| **OAuth 支援** | ❌ | ✅ |
| **Credential 管理** | 容器環境變數 | Auth Profile per-agent + SecretRef |

---

## 4️⃣ 記憶與學習

### NanoClaw
- **Memory 系統**：內建 Memory 模組，持久化 Agent 記憶
- **隔離記憶**：每個容器 Agent 擁有獨立記憶空間
- **記憶持久化**：基於容器的 storage volume

### OpenClaw
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Hybrid search**：向量 + FTS（全文搜尋）
- **Embedding provider**：可配置外部 embedding provider
- **Context compaction**：`compactEmbeddedPiSession` 自動壓縮長對話
- **Per-agent agentDir**：每個 Agent 獨立的 auth/model registry

### 對比總結

| 項目 | NanoClaw | OpenClaw |
|------|----------|----------|
| **Memory 類型** | ✅ 內建 Memory 模組 | ✅ MemoryIndexManager + Markdown |
| **隔離記憶** | ✅ 每容器獨立 | ✅ Per-agent `agentDir` |
| **向量搜尋** | ❌ | ✅ Embedding + hybrid search |
| **FTS 全文搜尋** | ❌ | ✅ FTS (hybrid) |
| **Context 壓縮** | ❌ | ✅ compactEmbeddedPiSession |

---

## 5️⃣ 工具與擴展

### NanoClaw
- **Scheduled Jobs**：內建排程系統，支援定時任務
- **Container-native tools**：工具運行在容器內，天然隔離
- **Anthropic SDK tools**：標準 tool-calling 模式

### OpenClaw
- **內建工具**：exec/process, code_execution, browser, web_search, read/write/edit, message, canvas, cron, image_generate
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation, group:messaging, group:nodes
- **Plugin tools**：Lobster(typed workflow), LLM Task, Diffs, OpenProse
- **CronService**：持久化 job storage, session scopes, heartbeat, webhook trigger
- **Image & Media**：image_generate(openai/fal/google), Whisper transcription, PDF, TTS
- **ACP & Sub-Agents**：sessions_spawn, ACPX Harness (外部 CLI 委派)

### 對比總結

| 項目 | NanoClaw | OpenClaw |
|------|----------|----------|
| **內建工具** | Anthropic SDK 標準工具 | 12+ 內建 + plugins |
| **排程系統** | ✅ Scheduled Jobs | ✅ CronService + heartbeat |
| **圖片生成** | ❌ | ✅ openai/fal/google |
| **TTS/STT** | ❌ | ✅ OpenAI/11Labs + Whisper |
| **Sub-agents** | ❌ 容器即隔離 | ✅ ACP + ACPX (外部 CLI) |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Tool Policy** | ❌ | ✅ allow/deny/profile/byProvider |
| **Media Pipeline** | ❌ | ✅ audio/video/PDF |

---

## 6️⃣ 多 Agent 架構

### NanoClaw
- **容器即 Agent**：每個 Agent = 獨立容器，天然隔離
- **Agent 間通訊**：透過 Gateway 路由消息
- **隔離保證**：文件系统、網路、記憶體完全隔離
- **擴展**：透過 Docker Compose/K8s 增加更多 Agent

### OpenClaw
- **ACP 協議**：Agent Control Protocol，parent-child session 隔離
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Multi-agent routing**：依 channel/account/peer/guild/team 路由
- **agentToAgent**：可選工具讓 agent 互傳訊息
- **Cross-agent QMD**：可跨 Agent 搜尋記憶
- **Sub-agent registry**：運行中的子代理註冊中心
- **Delivery Dispatch**：專門的子代理結果回傳系統

### 對比總結

| 項目 | NanoClaw | OpenClaw |
|------|----------|----------|
| **Agent 隔離** | ✅ 容器級隔離 | ⚠️ 需配置 Docker Sandbox |
| **子任務委派** | ❌ 需建立新容器 | ✅ ACP sessions_spawn/yield |
| **外部 CLI 委派** | ❌ | ✅ ACPX (Claude/Codex/Gemini/Kiro/Cursor) |
| **Agent 路由** | ✅ Gateway 路由 | ✅ Gateway 路由 (更精細) |
| **Agent 通訊** | ✅ Gateway 轉發 | ✅ agentToAgent tool |
| **遞迴保護** | ✅ 容器資源限制 | ✅ ACP 框架控制 |
| **Sub-agent Registry** | ❌ | ✅ |
| **Delivery Dispatch** | ❌ | ✅ |
| **跨 Agent 搜尋** | ❌ | ✅ Cross-agent QMD |

---

## 7️⃣ 安全性與沙盒

### NanoClaw
- **Security by container isolation**：安全模型基於 Docker 容器隔離
- **天然沙盒**：每個 Agent 的 filesystem、network、process 都隔離
- **容器級安全**：依賴 Docker 的安全功能
- **K8s 部署**：可進一步利用 Kubernetes security policies

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

| 項目 | NanoClaw | OpenClaw |
|------|----------|----------|
| **核心安全模型** | 容器隔離 | 多層 (Tool Policy + Sandbox + Audit) |
| **Container Isolation** | ✅ 預設啟用 | ⚠️ 需配置 |
| **Seccomp/AppArmor** | ⚠️ Docker 預設設定 | ✅ 可配置 profiles |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Tool Result Guard** | ❌ | ✅ |
| **SecretRef** | ❌ | ✅ SecretRef + encrypted auth-profile |
| **DM Policy** | ❌ | ✅ pairing/allowlist/open/disabled |
| **Security Audit** | ❌ | ✅ audit.ts / fix.ts |
| **Zod Validation** | ❌ | ✅ 拒絕未知 key |
| **K8s Pod Security** | ✅ (可選) | ❌ |

---

## 8️⃣ 平台與 Gateway

### NanoClaw
支援的通訊平台 (經 README 確認)：
- WhatsApp, Telegram, Slack, Discord, Gmail, 其他

### OpenClaw
支援 25+ 個平台 (含原生 App)：
- WhatsApp, Telegram, Slack, Discord, Signal, Matrix, Mattermost, Feishu, Google Chat, iMessage, IRC, MS Teams, LINE, Nostr, Twitch, WeChat, macOS/iOS/Android App, WebChat...

### 總結

| 項目 | NanoClaw | OpenClaw |
|------|----------|----------|
| **平台數量** | ~6+ | ~25+ |
| **共有平台** | WhatsApp, Telegram, Slack, Discord | 同左 + 更多 |
| **NanoClaw 獨有** | Gmail (natively) | — |
| **OpenClaw 獨有** | — | Signal, iMessage, Signal, Teams, LINE, Nostr, Twitch, WeChat, macOS/iOS/Android |
| **原生 App** | ❌ | ✅ macOS / iOS / Android |
| **Web Gateway** | ✅ (推斷) | ✅ WebChat + Control UI |
| **Voice Wake** | ❌ | ✅ |

---

## 9️⃣ 架構流程圖

### A. 核心架構對比

**NanoClaw: 容器隔離架構**
```text
User Input (Telegram/Discord/WhatsApp/Slack/Gmail)
   │
   ▼
[NanoClaw Gateway]
   │
   ├── 路由到 Agent A Container
   ├── 路由到 Agent B Container
   └── 路由到 Agent C Container
   │
   ▼
[Per-Agent Container]  ──► [Anthropic SDK Agent Loop]
   ├── Isolated Filesystem        │
   ├── Isolated Network           ├── LLM Call (Claude)
   ├── Isolated Memory            ├── Tool Execution
   └── Docker Resource Limits     └── Respond
```

**OpenClaw: Gateway + Agent Routing**
```text
Input (Channels) → Auth Layer(DM Policy) → Routing Engine(Bindings) → Per-Agent Runtime → LLM → Tool Policy Check → Sandbox → Respond
```

### B. 安全流程對比

**NanoClaw Security Flow**
```text
Input → Gateway → Route to Container → Agent Process (isolated) → Tool Call (container native)
                                                          ↓
                                                   Docker Isolation
                                                   (Filesystem/Network/Process)
```

**OpenClaw Security Flow**
```text
DM Policy → Tool Policy(allow/deny/profile) → Sandbox(Docker/seccomp/AppArmor) → SSRF → Tool Result Guard → SecretRef
```

### C. 多 Agent 隔離對比

| 特性 | NanoClaw | OpenClaw |
|------|----------|----------|
| **概念** | 每 Agent = 獨立容器 | 每 Agent = Workspace + Runtime |
| **隔離強度** | 🛡️🛡️🛡️🛡️🛡️ (頂級) | 🛡️🛡️🛡️ (可配置) |
| **擴展方式** | 加 Docker 容器 | 加 Agent config |
| **通訊** | Gateway 轉發 | Gateway + agentToAgent |

---

## 📊 總結對比表

| 維度 | NanoClaw 優勢 | OpenClaw 優勢 |
|------|---------------|---------------|
| **隔離強度** | ✅ 容器級隔離 — 預設安全 | ⚠️ 需手動配置 sandbox |
| **LLM 多樣性** | ❌ Anthropic 單一 | ✅ OpenAI/Anthropic/Google... |
| **通訊平台** | ⚠️ ~6+ 平台 | ✅ 25+ 平台 |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Memory** | ⚠️ 基礎 | ✅ 向量搜尋 + Hybrid |
| **排程** | ✅ Scheduled Jobs | ✅ CronService + heartbeat |
| **Sub-agents** | ❌ | ✅ ACP + ACPX |
| **開發門檻** | ⚠️ 需要 Docker | ⚠️ Node.js + 可選 Docker |
| **社群** | 26K+ ⭐, 39% fork率 | 57K+ ⭐ |
| **安全審計** | ❌ | ✅ audit.ts/fix.ts |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Voice/Media** | ❌ | ✅ TTS/STT/PDF/Image |

---

## 🎯 選哪個？

| 需求 | 推薦 |
|------|------|
| **最高等級的安全隔離** | ✅ NanoClaw |
| **多平台通訊覆蓋** | ✅ OpenClaw |
| **企業級多 Agent 部署** | ✅ NanoClaw (容器化) |
| **Anthropic Claude 原生整合** | ✅ NanoClaw |
| **最大 LLM 靈活性** | ✅ OpenClaw |
| **原生 macOS/iOS/Android App** | ✅ OpenClaw |
| **向量長記憶** | ✅ OpenClaw |
| **簡單排程任務** | ✅ NanoClaw |
| **外部 CLI 委派** | ✅ OpenClaw |
| **安全審計** | ✅ OpenClaw |
| **高 Fork 率 (社群活躍)** | ✅ NanoClaw (39% fork率) |
| **Voice Wake + Talk Mode** | ✅ OpenClaw |
