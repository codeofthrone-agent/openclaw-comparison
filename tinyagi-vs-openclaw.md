# TinyAGI vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：TinyAGI (TinyAGI/tinyagi), OpenClaw v2026.3.23+ | Repo: TinyAGI/tinyagi ⭐ 3,462
> 品牌演變：TinyClaw → TinyAGI (fka TinyClaw)

---

## 1️⃣ 核心定位

| 項目 | TinyAGI | OpenClaw |
|------|---------|----------|
| **開發者** | TinyAGI (社群) | 開源社群 |
| **設計理念** | **一人公司 Agent 團隊編排器** — Multi-agent, Multi-team, Multi-channel | 個人 AI 助理，Plugin 生態 + 外部 Agent 委派 (ACP) |
| **核心差異** | **Agent Teams 編排** — 管理多個 Agent 團隊，隔離工作區，24/7 全天候 | **Gateway 集中管理** — 多平台通訊 + ACP 委派外部 agent |
| **語言** | TypeScript | TypeScript (Node.js) |
| **Stars** | 3,462 ⭐ | ~57,000+ ⭐ |
| **Forks** | 491 | ~14,000+ |
| **License** | MIT | GPL-3.0 |
| **Open Issues** | 56（極低 — 成熟穩定） | 高 |
| **品牌演變** | TinyClaw → TinyAGI | — |
| **主要使用場景** | 一人公司、創作者、自由職業者、需要多角色 Agent 協同工作 | 個人 AI 助理、最大通訊平台覆蓋、多開發者 Agent 協作 |

---

## 2️⃣ 架構比較

### TinyAGI 架構

#### Agent Teams 編排架構
- **Multi-agent**：多個 Agent 各司其職，組成團隊
- **Multi-team**：支持多個團隊，每個團隊有獨立職責
- **Multi-channel**：跨通訊渠道整合
- **隔離工作區 (Isolated Workspace)**：每個 Agent 或團隊擁有獨立工作區
- **24/7 運行**：全天候 AI 助理體系
- **品牌演變**：從 TinyClaw 進化到 TinyAGI，名稱反映定位提升
- **成熟度**：僅 56 個 open issues，表明專案已高度穩定

#### 專案結構（推斷）
| 目錄/模組 | 功能 |
|-----------|------|
| `agents/` | Agent 定義和角色配置 |
| `teams/` | 團隊編排和協調邏輯 |
| `channels/` | 通訊渠道適配器 (Discord/WhatsApp/Telegram) |
| `workspaces/` | 隔離工作區管理 |
| `orchestrator/` | Agent 編排引擎 |
| `config/` | 配置管理 |

### OpenClaw 架構

#### Gateway + Agent Runtime 分離
- **Gateway**：WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**：Pi embedded runner (`runEmbeddedPiAgent`)
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**：channels, models, tools, skills, speech 可 plugin
- **ACP & ACPX**：委派外部 CLI agent (Claude Code/Codex/Gemini/Kiro/Cursor)

### 對比總結

| 項目 | TinyAGI | OpenClaw |
|------|---------|----------|
| **核心架構** | Agent Teams 編排 + 隔離工作區 | Gateway + Agent Runtime 分離 |
| **Multi-agent** | ✅ 核心功能 | ✅ 透過 ACP 協議 |
| **Multi-team** | ✅ **Unique — 團隊級編排** | ❌ (無團隊概念) |
| **Multi-channel** | ✅ | ✅ (25+ 平台) |
| **隔離工作區** | ✅ Agent/Team 級隔離 | ✅ Per-agent workspace |
| **Plugin 系統** | ❌ | ✅ Channels/Models/Tools/Skills |
| **ACP 協議** | ❌ (自建 Team 編排) | ✅ ACP + ACPX (外部 CLI) |
| **社群成熟度** | ✅ 56 issues (極低) | ⚠️ |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |

---

## 3️⃣ LLM Provider 支援

### TinyAGI
- **Anthropic**：Claude 系列模型
- **OpenAI**：GPT 系列模型
- **Google**：Gemini 系列模型
- **三大供應商**：涵蓋當前頂級 AI 供應商
- **配置方式**：推斷透過配置檔案設定不同 Agent 使用不同 Provider

### OpenClaw
- **多 Provider**：OpenAI, Anthropic, Google, Z.ai 等多個 provider
- **Auth Profiles**：每個 agent 有自己的 `auth-profiles.json`
- **Fallback chains**：primary → fallbacks（依順序）, provider auth failover
- **Custom providers**：`models.json` 可自訂 provider
- **OAuth 支援**：OpenAI Codex subscription, Anthropic setup-token
- **Model allowlist**：`agents.defaults.models` 定義可用模型
- **Provider normalization**：自動統一 lowercase + alias
- **Model catalog**：完整 Model 目錄 + aliases

### 對比總結

| 項目 | TinyAGI | OpenClaw |
|------|---------|----------|
| **Provider 數量** | 3 (Anthropic, OpenAI, Google) | 多 (含 Z.ai 及其他) |
| **頂級供應商** | ✅ 三家頂級全覆盖 | ✅ 同樣涵盖三家頂級 |
| **動態切換** | ⚠️ | ✅ 透過 /model command |
| **Fallback chains** | ⚠️ | ✅ Primary + fallbacks |
| **Custom endpoint** | ⚠️ | ✅ Custom providers in models.json |
| **OAuth 支援** | ❌ | ✅ |
| **Credential 管理** | API Key 配置 | Auth Profile + SecretRef |
| **Provider Normalization** | ❌ | ✅ lowercase + alias |
| **Model allowlist** | ❌ | ✅ |

---

## 4️⃣ 記憶與學習

### TinyAGI
- **隔離工作區記憶**：每個 Agent/Team 擁有獨立的記憶空間
- **團隊共享記憶**：推斷支援 Team 級別的共享記憶
- **跨 Agent 知識**：可能透過工作區共享實現知識轉移

### OpenClaw
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Hybrid search**：向量 + FTS（全文搜尋）
- **Embedding provider**：可配置外部 embedding provider
- **Context compaction**：`compactEmbeddedPiSession` 自動壓縮長對話
- **Per-agent agentDir**：每個 Agent 獨立的 auth/model registry
- **Cross-agent QMD**：可配置讓 Agent A 搜尋 Agent B 的會話
- **Skills System**：Directory-based skills (SKILL.md + env + auto-discovery)

### 對比總結

| 項目 | TinyAGI | OpenClaw |
|------|---------|----------|
| **Memory 類型** | 隔離工作區記憶 | MemoryIndexManager + Markdown |
| **隔離記憶** | ✅ Agent/Team 級隔離 | ✅ Per-agent agentDir |
| **向量搜尋** | ❌ | ✅ Embedding + hybrid search |
| **FTS 全文搜尋** | ❌ | ✅ FTS (hybrid) |
| **Context 壓縮** | ❌ | ✅ compactEmbeddedPiSession |
| **跨 Agent 搜尋** | ⚠️ 推斷工作區共享 | ✅ Cross-agent QMD |
| **Skill 系統** | ❌ | ✅ SKILL.md + 自動發現 |

---

## 5️⃣ 工具與擴展

### TinyAGI
- **Agent 工具集**：每個 Agent 有專屬工具集
- **團隊協作工具**：推斷支援 Agent 間任務分派和協調
- **工作區工具**：隔離工作區內的檔案和執行操作
- **擴展性**：透過 TypeScript 生態添加自定義工具

### OpenClaw
- **內建工具**：exec/process, code_execution, browser, web_search, read/write/edit, message, canvas, cron, image_generate
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation, group:messaging, group:nodes
- **Plugin tools**：Lobster(typed workflow), LLM Task, Diffs, OpenProse
- **CronService**：持久化 job storage, session scopes, heartbeat, webhook trigger
- **Image & Media**：openai/fal/google image gen, Whisper transcription, PDF, TTS
- **ACP & Sub-Agents**：sessions_spawn, ACPX Harness

### 對比總結

| 項目 | TinyAGI | OpenClaw |
|------|---------|----------|
| **內建工具** | ⚠️ Agent 級工具集 | ✅ 12+ 內建 + plugins |
| **Cron/排程** | ⚠️ | ✅ CronService + heartbeat |
| **圖片生成** | ❌ | ✅ openai/fal/google |
| **TTS/STT** | ❌ | ✅ OpenAI/11Labs + Whisper |
| **Sub-agents** | ✅ Agent Teams | ✅ ACP + ACPX |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Media Pipeline** | ❌ | ✅ audio/video/PDF |
| **Browser 自動化** | ⚠️ | ✅ |
| **Plugin 工具** | ❌ | ✅ 豐富的 plugin 生態 |

---

## 6️⃣ 多 Agent 架構

### TinyAGI
- **Multi-agent**：多個 Agent，各自有專長領域
- **Multi-team**：**Unique feature** — 多個團隊，各有職責範圍
- **Multi-channel**：跨通訊渠道整合
- **隔離工作區**：每個 Agent/Team 獨立工作區，互不干擾
- **Team 編排**：Agent 編排引擎協調不同 Agent 和團隊的工作
- **一人公司場景**：專注於「一個人的軍隊」概念

### OpenClaw
- **ACP 協議**：Agent Control Protocol，parent-child session 隔離
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Multi-agent routing**：依 channel/account/peer/guild/team 路由
- **agentToAgent**：可選工具讓 agent 互傳訊息
- **Cross-agent QMD**：可跨 Agent 搜尋記憶
- **Sub-agent registry** & **Delivery Dispatch**

### 對比總結

| 項目 | TinyAGI | OpenClaw |
|------|---------|----------|
| **Multi-agent** | ✅ 核心功能 | ✅ ACP 協議 |
| **Multi-team** | ✅ **Unique — 團隊級編排** | ❌ (無此概念) |
| **Multi-channel** | ✅ | ✅ |
| **隔離工作區** | ✅ 核心功能 | ✅ Per-agent workspace |
| **Agent 路由** | ✅ Team 編排路由 | ✅ Gateway bindings 路由 |
| **外部 CLI 委派** | ❌ | ✅ ACPX (Claude/Codex/Gemini/Kiro/Cursor) |
| **Agent 通訊** | ✅ Team 內協作 | ✅ agentToAgent tool |
| **跨 Agent 搜尋** | ⚠️ | ✅ Cross-agent QMD |
| **子會話管理** | ❌ | ✅ sessions_spawn/yield |
| **Delivery Dispatch** | ❌ | ✅ |
| **適合場景** | 一人公司、多角色 Agent 團隊 | 通訊 + 開發、多 CLI agent 委派 |

---

## 7️⃣ 安全性與沙盒

### TinyAGI
- **隔離工作區安全**：每個 Agent/Team 有獨立工作區 → 天然隔離
- **TypeScript 執行安全**：依賴 Node.js 生態
- **Agent 權限隔離**：推斷不同 Agent 有不同的權限範圍
- **成熟度**：56 個 open issues → 已高度穩定，安全漏洞較少

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
- **Capability Model**：定義 channel/agent 允許的操作

### 對比總結

| 項目 | TinyAGI | OpenClaw |
|------|---------|----------|
| **核心安全模型** | 隔離工作區 (天然隔離) | 多層 (Tool Policy + Sandbox + Audit) |
| **Workspace Isolation** | ✅ Agent/Team 級隔離 | ✅ Per-agent + Per-session |
| **Container Sandbox** | ❌ | ✅ Docker / SSH |
| **Seccomp/AppArmor** | ❌ | ✅ profiles |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Tool Result Guard** | ❌ | ✅ |
| **SecretRef** | ❌ | ✅ SecretRef + encrypted auth-profile |
| **DM Policy** | ❌ | ✅ pairing/allowlist/open/disabled |
| **Security Audit** | ❌ | ✅ audit.ts / fix.ts |
| **Zod Validation** | ❌ | ✅ |
| **Capability Model** | ❌ | ✅ 定義操作權限 |
| **專案成熟度** | ✅ 56 issues (穩定) | ⚠️ |

---

## 8️⃣ 平台與 Gateway

### TinyAGI
支援 3 個核心通訊平台：
- **Discord** — 社群/團隊溝通
- **WhatsApp** — 個人/商業訊息
- **Telegram** — 群組/Bot 平台

### OpenClaw
支援 25+ 個通訊平台：
- WhatsApp, Telegram, Slack, Discord, Signal, Matrix, Mattermost, Feishu, Google Chat, iMessage, IRC, MS Teams, LINE, Nostr, Twitch, WeChat, macOS/iOS/Android App, WebChat...

### 總結

| 項目 | TinyAGI | OpenClaw |
|------|---------|----------|
| **平台數量** | 3 | ~25+ |
| **共有平台** | Discord, WhatsApp, Telegram | 同左 + 更多 |
| **TinyAGI 獨有** | — | — |
| **OpenClaw 獨有** | — | Slack, Signal, iMessage, Teams, LINE, Nostr, WeChat... |
| **原生 App** | ❌ | ✅ macOS / iOS / Android |
| **Web Gateway** | ❌ | ✅ WebChat + Control UI |
| **Voice Wake** | ❌ | ✅ |
| **平台策略** | 精選 3 大主流 | 最大覆蓋 |

---

## 9️⃣ 架構流程圖

### A. 核心架構對比

**TinyAGI: Agent Teams 編排架構**
```text
User Input (Discord / WhatsApp / Telegram)
   │
   ▼
[TinyAGI Orchestrator]
   │
   ├── Team A (開發團隊)
   │   ├── Developer Agent
   │   ├── Reviewer Agent
   │   └── Deployer Agent
   │       └── Isolated Workspace A
   │
   ├── Team B (營運團隊)
   │   ├── Marketing Agent
   │   ├── Analytics Agent
   │   └── Content Agent
   │       └── Isolated Workspace B
   │
   └── Team C (客服團隊)
       ├── Support Agent
       └── Escalation Agent
           └── Isolated Workspace C
```
- 一人公司 = 多個 Agent Team = 完整組織
- 每個 Team 有獨立工作區和角色分工
- Orchestrator 負責跨 Team 協調

**OpenClaw: Gateway + Agent Routing**
```text
Input (Channels) → Auth Layer(DM Policy) → Routing Engine(Bindings) → Per-Agent Runtime → LLM → Tool Policy Check → Sandbox → Respond
```

### B. 安全流程對比

**TinyAGI Security Flow**
```text
Input → Channel Adapter → Orchestrator → Route to Team/Agent → Isolated Workspace → Execute → Respond
                                          ↑                                 ↑
                                Platform Auth                        Workspace Isolation
```

**OpenClaw Security Flow**
```text
DM Policy → Tool Policy(allow/deny/profile) → Sandbox(Docker/seccomp/AppArmor) → SSRF → Tool Result Guard → SecretRef
```

### C. 多 Agent 編排對比

| 維度 | TinyAGI | OpenClaw |
|------|---------|----------|
| **編排方式** | Team-based Orchestrator | ACP Protocol + Gateway Routing |
| **隔離單位** | Workspace per Agent/Team | Workspace per Agent |
| **Agent 角色** | 預定義角色 (開發/營運/客服) | 通用 Agent |
| **通訊** | Team 內 + Cross-team | agentToAgent tool |
| **外部委派** | ❌ | ✅ ACPX (外部 CLI) |
| **適合場景** | 一人公司 (One Person Company) | 個人開發 + 通訊平台管理 |

---

## 📊 總結對比表

| 維度 | TinyAGI 優勢 | OpenClaw 優勢 |
|------|--------------|---------------|
| **Team 級編排** | ✅ **Unique — Multi-team 架構** | ❌ |
| **一人公司定位** | ✅ 完整組織模擬 | ❌ |
| **隔離工作區** | ✅ Agent/Team 級隔離 | ✅ Per-agent workspace |
| **專案成熟度** | ✅ 56 issues (極穩定) | ⚠️ |
| **MIT 授權** | ✅ | ❌ GPL-3.0 |
| **通訊平台數量** | ⚠️ 3 大主流 | ✅ 25+ 全面覆蓋 |
| **外部 CLI 委派** | ❌ | ✅ ACPX (Claude/Codex/Gemini/Kiro/Cursor) |
| **向量記憶搜尋** | ❌ | ✅ Embedding + Hybrid |
| **安全審計** | ❌ | ✅ audit.ts/fix.ts |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Plugin 生態** | ❌ | ✅ 完整的 plugin 系統 |

---

## 🎯 選哪個？

| 需求 | 推薦 |
|------|------|
| **一人公司 — 完整組織模擬** | ✅ TinyAGI (核心定位) |
| **Multi-team Agent 編排** | ✅ TinyAGI (Unique) |
| **隔離工作區管理** | ✅ TinyAGI (Team 級) |
| **極高穩定性 (56 issues)** | ✅ TinyAGI |
| **MIT 授權 (商業友好)** | ✅ TinyAGI |
| **最大通訊平台覆蓋 (25+)** | ✅ OpenClaw |
| **外部 CLI 委派工具 (ACPX)** | ✅ OpenClaw |
| **向量長記憶 + 向量搜尋** | ✅ OpenClaw |
| **安全審計 + Tool Policy** | ✅ OpenClaw |
| **原生 macOS/iOS/Android App** | ✅ OpenClaw |
| **Plugin 生態系統** | ✅ OpenClaw |
| **Workflow DSL (Lobster)** | ✅ OpenClaw |
| **多 LLM Provider Fallback** | ✅ OpenClaw |
