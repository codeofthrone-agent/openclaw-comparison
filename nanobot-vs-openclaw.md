# NanoBot vs OpenClaw —— 深度比較

> 整理日期：2026-04-04 | 版本：NanoBot (HKUDS/nanobot), OpenClaw v2026.3.23+

---

## 1️⃣ 核心定位

| 項目 | NanoBot | OpenClaw |
|------|---------|----------|
| **開發者** | HKUDS (香港大學資料科學實驗室) | 開源社群 |
| **設計理念** | Ultra-Lightweight Personal AI Assistant — 代碼量比 OpenClaw 少 99%，極簡設計 | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **核心差異** | 單一 Python 套件，`pip install` 即可使用，極低門檻 | Monorepo 架構，多層 Gateway + Agent Runtime + 複雜設定 |
| **主要使用場景** | 輕量化個人助理、快速部署、MCP 工具擴展、Cron 排程、團隊協作 | 程式開發、自動化工作流、多 agent 協作、原生 App 控制、預測市場整合 |
| **GitHub** | ⭐ 37,801 / 🍴 6,552 / MIT License | ⭐ 37,800+ / 社群驅動 / MIT |
| **語言** | Python 3.11+ | TypeScript / Node.js |
| **安裝** | `pip install nanobot-ai` | npm install / brew install / 手動編譯 |
| **DeepWiki** | 9 章 | 10 章 62 節 |
| **Open Issues** | 927 | 社群追蹤 |
| **PRs** | 558 | 社群追蹤 |

---

## 2️⃣ 架構比較

### NanoBot（經 DeepWiki 確認）

#### 輕量化核心架構
- **單一執行引擎**：Python 原生 asyncio event loop，無多進程分離
- **Socket.IO 通訊**：所有平台透過統一的 Socket.IO adapter 層
- **Plugin-driven**：以 MCP server 為核心擴展機制，非內建大量工具
- **最小化設計**：代碼量極小，核心邏輯集中在 `nanobot/` 目錄下

#### 專案結構（推斷）
| 目錄 | 功能 |
|------|------|
| `nanobot/core/` | 核心引擎、agent loop、prompt 組裝 |
| `nanobot/platforms/` | 12 個通訊平台 adapter |
| `nanobot/memory/` | Token-based 記憶系統 |
| `nanobot/tools/` | MCP 客戶端 + 內建工具 |
| `nanobot/cron/` | 排程器 |
| `nanobot/subagents/` | Sub-agent 調度 |
| `nanobot/docker/` | Docker 沙盒整合 |

---

### OpenClaw（經 DeepWiki 1.3 確認）

#### Gateway + Agent Runtime 分離
- **Gateway**: WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**: Pi embedded runner (`runEmbeddedPiAgent`)，獨立於 Gateway 的 AI 執行引擎
- **Multi-agent routing**: 依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**: channels, models, tools, skills, speech, image gen 都可透過 plugin 擴展

#### 平台架構特點
- **Node 系統**: macOS/iOS/Android 原生 app 作為 Device Node
- **Device Node Protocol**: WebSocket-based RPC，暴露 camera/screen/location/notification
- **Live Canvas (A2UI)**: Agent 可渲染交互式 UI canvas
- **Voice Wake + Talk Mode**: always-on 語音喚醒（macOS/iOS/Android）

---

### 對比總結

| 項目 | NanoBot | OpenClaw |
|------|---------|----------|
| **核心架構** | 單體式、單一 event loop | Gateway + Agent Runtime + Extensions |
| **Agent 引擎** | Python asyncio | Pi embedded runner (runEmbeddedPiAgent) |
| **Plugin 系統** | MCP server 為核心 | 全方位 Plugin (channels/models/tools/skills/speech) |
| **代碼量** | 極輕量（比 OpenClaw 少 99%） | 龐大 Monorepo |
| **輔助模型** | ❌ | ❌ |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |
| **Device Node** | ❌ | ✅ Camera/Screen/Location |
| **Gateway UI** | ❌ | ✅ Control UI + WebChat (localhost:18789) |

---

## 3️⃣ LLM Provider 支援

### NanoBot

#### Provider 設定
- **SDK 支援**：直接透過 OpenAI SDK + Anthropic SDK 對接
- **Provider 配置**：透過 config 設定 `base_url` 和 `api_key`，支援 any OpenAI-compatible API
- **模型選擇**：config 中指定 `model` 字段，支援動態切換
- **Fallback**：基礎的 retry 機制（SDK 層級）

#### 特點
- **Python SDK 原生**：利用 OpenAI/Anthropic 官方 SDK 的內建功能
- **OpenAI-compatible**：任何 OAI-compatible endpoint 都可使用（vLLM, Ollama, LM Studio...）
- **無 Smart Routing**：無自動模型降級或智能路由
- **無憑證池**：單一 API key 設定

---

### OpenClaw（經 DeepWiki 3.3 確認）

#### Model Providers（DeepWiki 3.3）
- **Auth Profiles**: 每個 agent 有自己的 `auth-profiles.json`
- **Provider synchronization**: `openclaw.json` ↔ `models.json` 自動同步
- **Credential hierarchy**: 環境變數 → config → auth-profiles 層級解析
- **OAuth 支援**: OpenAI Codex subscription (OAuth), Anthropic (API key / setup-token)
- **Model catalog / allowlist**: `agents.defaults.models` 定義可用模型（含 aliases）
- **Fallback chains**: primary → fallbacks（依順序），provider auth failover 在移動到下一個 model 前

#### Provider Normalization（DeepWiki 3.3.1）
- Model refs 正規化為 lowercase (`provider/model` 格式)
- Provider aliases (`z.ai/*` → `zai/*`)

---

### 對比總結

| 項目 | NanoBot | OpenClaw |
|------|---------|----------|
| **Provider 數量** | 基礎（OpenAI + Anthropic SDK + OAI-compatible） | 多家（透過 OAuth + API Key + plugin） |
| **動態切換** | ✅ config 修改即可 | ✅ 透過 /model command |
| **憑證池** | ❌ | ❌ Auth Profile per-agent |
| **智慧路由** | ❌ | ❌ |
| **Auxiliary Client** | ❌ | ❌ |
| **Fallback chains** | ✅ SDK retry | ✅ Primary + fallbacks |
| **Custom endpoint** | ✅ OAI-compatible | ✅ Custom providers in models.json |
| **Model allowlist** | ❌ | ✅ agents.defaults.models |
| **Provider normalization** | ❌ | ✅ lowercase + alias |
| **OAuth** | ❌ | ✅ OpenAI Codex / Anthropic |

---

## 4️⃣ 記憶與學習機制

### NanoBot

#### Token-based 記憶系統
- **Token 計數管理**：以 token 數量為基準，控制歷史保留長度
- **自動淘汰**：超過 token 上限時自動截斷或壓縮歷史
- **對話狀態追蹤**：維持當前会话的上下文窗口
- **輕量設計**：無複雜索引、無向量資料庫、無 hybrid search

#### 特點
- **簡單直接**：沒有 MEMORY.md/USER.md 等額外的持久化檔案格式
- **無外部 Memory Provider**：不依賴 Honcho/Mem0 等外部服務
- **無向量搜尋**：不做 embedding-based 檢索
- **Cron + Subagents 狀態**：透過任務 ID 追蹤執行狀態

---

### OpenClaw（經 DeepWiki 確認）

#### Memory 系統（DeepWiki 3.4.3: Memory & Search）
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Embedding provider selection**：可配置外部 embedding provider
- **Hybrid search**: 向量 + FTS（全文搜尋）
- **Sync strategies**：記憶體與 workspace 同步策略
- **Per-agent `agentDir`**：每個 agent 的 auth-profiles.json 和 model registry

#### Context Compaction（DeepWiki 3.6）
- **`compactEmbeddedPiSession`**：自動壓縮長對話歷史
- **Token 限制管理**：當 token count 接近 context window 時自動總結
- **Preserves tool results**：保留工具執行結果的連續性

#### Skills System（DeepWiki 5.2）
- **Directory-based skills**：每個 skill 是 `SKILL.md` + 可選腳本/模板
- **Environment injection**：skill 可注入環境變數、依賴檢查
- **Per-agent vs shared**：`skills/` 在 workspace 是 per-agent，`~/.openclaw/skills` 是共享
- **Auto-discovery**：agent 啟動時自動發現可用 skills

---

### 對比總結

| 項目 | NanoBot | OpenClaw |
|------|---------|----------|
| **Memory 類型** | ✅ Token-based，簡單上下文管理 | ✅ MemoryIndexManager + Markdown |
| **向量搜尋** | ❌ | ✅ Embedding + hybrid search |
| **FTS 全文搜尋** | ❌ | ✅ FTS (hybrid) |
| **每輪自動注入** | ❌ | ✅ 需 sync triggers |
| **Session 持久化** | ✅ JSON/SQLite（輕量） | ✅ JSONL transcript files |
| **Context 壓縮** | ✅ Token 上限自動截斷 | ✅ compactEmbeddedPiSession |
| **Honcho/Mem0 集成** | ❌ | ✅ Plugin 生態 |
| **Skill 格式** | ❌ SKILL.md 未知 | ✅ SKILL.md (Markdown + env vars) |
| **Skills Hub** | ❌ | ✅ ClawHub + npm |
| **Agent 自主安裝** | ❌ | ✅ 可透 plugin |
| **Per-agent 隔離** | 基礎 | ✅ via workspace agentDir |

---

## 5️⃣ 工具與擴展

### NanoBot

#### MCP 為核心的擴展機制
- **MCP Client**：原生整合 MCP (Model Context Protocol)，連接任意 MCP Server
- **內建工具**：基礎工具（檔案讀寫、終端執行、網頁抓取等）
- **Docker 隔離**：工具執行可選 Docker 容器
- **Cron 排程**：定期任務，支援 cron 表達式和 interval

#### Subagent 系統
- **Subagent 委派**：可將任務委派給子代理執行
- **隔離執行**：子代理有獨立的上下文和資源
- **平行執行**：支援多個 subagent 同時運行
- **結果匯總**：父代理收集並整合子代理結果

#### 技術棧特色
- **Python 3.11+**：現代 Python 功能（match-case, asyncio）
- **OpenAI/Anthropic SDK**：原生 SDK 而非自造輪子
- **Socket.IO**：統一通訊層

---

### OpenClaw

#### Tools System（DeepWiki 3.4）
- **內建工具**：exec/process, code_execution, browser, web_search/x_search/web_fetch, read/write/edit, apply_patch, message, canvas, nodes, cron/gateway, image/image_generate, sessions_*/agents_list
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation, group:messaging, group:nodes, group:openclaw
- **Plugin tools**：Lobster(typed workflow), LLM Task(structured JSON), Diffs, OpenProse

#### Automation & Cron（DeepWiki 3.7）
- **CronService**：持久化 job storage
- **Session scopes**：main / isolated / current session
- **Heartbeat**：定期 agent interaction
- **Webhook**：可配置 webhook trigger
- **Pre-execution hooks**：job 前可跑 hook

#### Image & Media（DeepWiki 3.4.6）
- **image_generate**：openai/fal/google provider
- **Audio/video**：Whisper transcription
- **PDF processing**
- **TTS/Voice**：OpenAI TTS, ElevenLabs streaming

#### ACP & Sub-Agents（DeepWiki 3.8）
- **ACP**：Agent Control Protocol — parent-child session 隔離 + delivery dispatch
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派給 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Async reporting**：子 agent 非同步回報結果

---

### 對比總結

| 項目 | NanoBot | OpenClaw |
|------|---------|----------|
| **工具數量** | 基礎內建 + MCP 擴展 | 12+ 內建 + plugins |
| **擴展機制** | ✅ MCP Server | ✅ Plugin system + ClawHub |
| **Execution 後端** | ✅ Docker（局部） | ✅ Docker(seccomp/AppArmor)/SSH/native |
| **Cron** | ✅ cron 表達式 + interval | ✅ CronService + session scopes + heartbeat |
| **TTS/STT** | ❌ | ✅ OpenAI/11Labs TTS + Whisper STT |
| **圖片生成** | ❌ | ✅ image_generate(openai/fal/google) |
| **Sub-agents** | ✅ Subagent 系統 | ✅ ACP + ACPX(external CLI), async |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Agent-to-Agent** | ❌ | ✅ agentToAgent tool (可啟用) |
| **Voice Wake** | ❌ | ✅ 原生 App always-on |
| **Canvas A2UI** | ❌ | ✅ |
| **Media Pipeline** | ❌ | ✅ audio/video/PDF |

---

## 6️⃣ 多 Agent 架構

### NanoBot

#### Subagent 系統
- **Subagent 委派**：父代理可將任務委派給子代理
- **上下文隔離**：子代理有獨立的對話上下文
- **平行執行**：支援多個 subagent 同時運行
- **結果匯總**：父代理收集子代理結果並整合到回覆
- **簡單輕量**：沒有複雜的 protocol 或 registry
- **限制**：子 agent 數量無明確上限說明，但受資源約束

---

### OpenClaw（經 DeepWiki 3.8, 2.5 確認）

#### ACP (Agent Control Protocol)
- **Parent-Child Session**: 支持子會話生命周期管理（`sessions_spawn` / `sessions_yield` / `agents_list`）
- **Sub-agent registry**: 註冊中心管理運行中的子代理
- **Delivery Dispatch**: 專門的傳遞系統處理子代理結果回傳
- **Multi-agent routing**: 依 channel/account/peer/guild/team 路由到隔離 agent
- **agentToAgent**: 可選工具讓 agent 之間互相發送訊息（預設關閉）
- **Cross-agent QMD**: `memorySearch.qmd.extraCollections` 允許 Agent A 搜尋 Agent B 的會話
- **ACPX Harness**: 委派任務給外部 AI CLI (Claude Code, Codex, Gemini, Kiro, Cursor)
    - 每個外部 CLI 有獨立的 auth、tool use、LLM API
    - 透過 stdin/stdout JSON-RPC 通訊

---

### 對比總結

| 項目 | NanoBot | OpenClaw |
|------|---------|----------|
| **子任務委派** | ✅ Subagent 系統 | ✅ ACP 協議 — sessions_spawn/yield + ACPX 委派 |
| **外部 CLI 委派** | ❌ | ✅ ACPX Harness (Claude/Codex/Gemini/Kiro/Cursor) |
| **子會話管理** | 基本上下文隔離 | ✅ sessions_spawn / sessions_yield / agents_list |
| **Agent Registry** | ❌ | ✅ ACP sub-agent registry |
| **遞迴保護** | 基礎 | ✅ 由外部 CLI / ACP 控制 |
| **Context 隔離** | ✅ 獨立 | ✅ parent-child session |
| **Multi-Agent 路由** | ❌ | ✅ 依 channel/account/peer 自動分流 |
| **Agent 通訊** | ❌ | ✅ agentToAgent tool (可選) |
| **跨 Agent 搜尋** | ❌ | ✅ Cross-agent QMD search |
| **Delivery Dispatch** | 基本結果匯總 | ✅ ACP managed delivery |
| **完整進程模式** | ❌ | ✅ Claude Code / Codex / Gemini CLI 進程 |
| **Git Worktree** | ❌ | ✅ 透過外部 CLI |

---

## 7️⃣ 安全性與沙盒

### NanoBot

#### 基礎安全機制
- **Docker 沙盒**：工具執行可選 Docker 容器隔離
- **API Key 管理**：透過 config 設定，基礎環境變數隔離
- **簡單訪問控制**：各平台 adapter 有基礎的認證和授權
- **輕量設計**：無複雜的安全審計或策略引擎

#### 特點
- **最小化攻擊面**：代碼量少意味著潛在漏洞較少
- **無複雜策略**：沒有多層安全策略引擎
- **依賴 SDK 安全**：依賴 OpenAI/Anthropic SDK 的基礎安全機制

---

### OpenClaw（經 DeepWiki + `src/security/` 確認）

#### Sandbox 架構
- **Sandbox 後端**：Docker / SSH / 原生 OS 隔離
- **seccomp / AppArmor**：進程級安全策略
- **Workspace 隔離**：獨立 workspace root，per-session workspaces
- **嚴格檔案系統和網路規則**

#### 安全機制
- **DM Policy**：`pairing` / `allowlist` / `open` / `disabled`（預設 pairing）
- **Tool Policy**：`tools.allow` / `tools.deny`（deny 優先）+ `tools.profile` + `tools.byProvider`
- **Tool Result Guard**：`guardSessionManager` 驗證工具輸出形狀與策略
- **SecretRef**：敏感資料動態解析，encrypted auth-profile
- **Security Audit**：`audit.ts` / `audit-fs.ts` / `audit-extra.ts` / `fix.ts`
- **Zod Schema 驗證**：嚴格的 config 驗證，未知 key 直接拒絕啟動
- **Capability Model**：定義 channel/agent 允許的操作

---

### 對比總結

| 項目 | NanoBot | OpenClaw |
|------|---------|----------|
| **Sandbox 後端** | ✅ Docker（局部） | ✅ Docker / SSH / 原生 OS |
| **危險指令 Regex 掃描** | ❌ | ❌ |
| **Tirith 整合** | ❌ | ❌ |
| **Seccomp/AppArmor** | ❌ | ✅ profiles |
| **SSRF Protection** | 基礎 | ✅ URL allowlists/deny lists |
| **Website Blocklist** | ❌ | ❌ |
| **Tool Policy** | ❌ | ✅ allow/deny/profile/byProvider |
| **Tool Result Guard** | ❌ | ✅ 驗證輸出形狀 |
| **DM Policy** | 基礎 | ✅ pairing/allowlist/open/disabled |
| **Security Audit** | ❌ | ✅ audit.ts / audit-fs.ts / fix.ts |
| **SecretRef** | 基礎 config | ✅ SecretRef + encrypted auth-profile |
| **Zod Schema Validation** | ❌ | ✅ 拒絕未知 key 啟動 |
| **Capability Model** | ❌ | ✅ 定義操作權限 |
| **最小攻擊面** | ✅ 代碼量極少 | ❌ |

---

## 8️⃣ 平台與 Gateway

### NanoBot — 12 個通訊平台

| 平台 | 備註 |
|------|------|
| **Telegram** | ✅ |
| **Discord** | ✅ |
| **WhatsApp** | ✅ |
| **LINE**（微信） | ✅ |
| **Feishu（飛書）** | ✅ |
| **DingTalk（釘釘）** | ✅ |
| **Slack** | ✅ |
| **Matrix** | ✅ |
| **Email** | ✅ |
| **QQ** | ✅ |
| **WeCom（企業微信）** | ✅ |
| **Mochat** | ✅ |

### OpenClaw (官方 README 列出的 Channels) — 共 25+ 個平台

NanoBot 有 12 個平台，OpenClaw 有 25+ 個平台。

| 項目 | NanoBot | 備註 |
|------|---------|------|
| 兩者共有的平台 | Discord, Telegram, WhatsApp, Slack, Matrix, Feishu, DingTalk, WeCom, Email | 9 個重疊 |
| NanoBot 獨有 | LINE（微信）, QQ, Mochat | 3 個 |
| OpenClaw 獨有 | Signal, Mattermost, Google Chat, BlueBubbles, iMessage, IRC, Teams, LINE, Nextcloud Talk, Nostr, Synology Chat, Tlon, Twitch, Zalo, WeChat | 15+ 個 |
| 原生 App | ❌ | ✅ macOS / iOS / Android |
| 特色功能 | 無 | Live Canvas, Voice Wake, Talk Mode |

---

### 總結摘要

| 維度 | NanoBot | OpenClaw |
|------|---------|----------|
| **平台總數** | ~12 | ~25+ (含原生 App) |
| **兩者共有的平台** | Discord, Telegram, WhatsApp, Slack, Matrix, Feishu, DingTalk, WeCom, Email | 同左 |
| **NanoBot 獨有** | LINE（微信）, QQ, Mochat | — |
| **OpenClaw 獨有** | — | Signal, Mattermost, Google Chat, BlueBubbles, iMessage, IRC, Teams, Nextcloud Talk, Nostr, Synology Chat, Tlon, Twitch, Zalo, WeChat, macOS/iOS/Android App |
| **原生 App** | ❌ | ✅ macOS / iOS / Android |
| **特色功能** | 中國企業通訊整合 | Live Canvas, Voice Wake, Talk Mode |

---

## 9️⃣ 架構與流程圖解

### A. 核心架構對比

**NanoBot: 輕量單體架構 (Lightweight Monolithic)**
```text
Input (Telegram/Discord/WhatsApp/Feishu/DingTalk/Slack...)
   │
   ▼
[Platform Adapter (Socket.IO)]
   │
   ▼
[NanoBot Core Engine]
   ├── 1. Build Context ──► Memory (Token-based)
   ├── 2. LLM Call ───────► OpenAI/Anthropic SDK
   ├── 3. Tool Dispatch:
   │    ├── [MCP Servers] ─▶ 連接外部 MCP
   │    ├── [Subagents] ───▶ 平行子代理執行
   │    ├── [Cron] ───────▶ 定期任務
   │    └── [Docker] ─────▶ 容器化執行
   └── 4. Respond
```

**OpenClaw: 分散式網關 + Agent 路由 (Gateway + Routing)**
```text
Input (Channels: WhatsApp / Telegram / Discord / Signal / iMessage)
   │
   ▼
[Auth Layer] (DM Policy: Pairing / Allowlist / Open)
   │
   ▼
[Routing Engine] ──► Match Binding (Channel / Account / Peer / Guild)
   │
   ▼
[Per-Agent Runtime] (Workspace + Auth + Sessions)
   ├── 1. Context: Workspace (AGENTS.md, SOUL.md) + Memory Plugin
   ├── 2. Model: Primary → Fallback (Auth Rotation)
   ├── 3. Tool Policy: tools.allow/deny → Sandbox Check → Exec
   ├── 4. Sub-Agents: ACP (sessions_spawn) + ACPX Harness (External CLI)
   └── 5. Result Guard (Validate Output) ──► Reply
```

---

## 📊 總結對比

| 維度 | NanoBot 優勢 | OpenClaw 優勢 |
|------|-------------|---------------|
| **輕量** | **代碼量少 99%**、`pip install` 秒裝、Python 單一套件 | Monorepo，設定複雜 |
| **跨平台** | 12 通訊平台，**中國企業通訊完整**（微信/飛書/釘釘/QQ/企業微信） | **25+ 通訊平台**，原生 App (macOS/iOS/Android) |
| **擴展** | MCP 標準生態 | 全方位 Plugin + ClawHub |
| **LLM Provider** | OpenAI/Anthropic SDK + OAI-compatible 通用 | 多 provider + OAuth + fallback chains + auth profiles |
| **記憶** | Token-based（簡單直接） | **向量搜尋 + Hybrid search + MemoryIndex** |
| **多 Agent** | Subagent 系統（輕量） | **ACP + ACPX + sessions_spawn + multi-agent routing** |
| **Cron** | ✅ cron 表達式 + interval | ✅ CronService + heartbeat + webhooks |
| **安全** | **最小攻擊面**（代碼少） | **全面安全機制**（seccomp, audit, zod, tool guard） |
| **工具生態** | MCP Server 擴展 | 內建 12+ 工具 + Plugin 工具 + Lobster DSL |
| **適合誰** | **快速部署**、Python 開發者、**中國企業**、輕量化需求 | **生產環境**、多平台整合、**原生 App**、**高安全需求** |

---

## 🎯 選哪個？

| 需求 | 推薦 |
|------|------|
| 最快上手，一行 `pip install` | ✅ NanoBot |
| 最小代碼量，最小攻擊面 | ✅ NanoBot |
| Python 環境，Python 開發者 | ✅ NanoBot |
| 中國企業通訊（微信/飛書/釘釘/QQ/企業微信/Mochat） | ✅ NanoBot |
| MCP 標準擴展 | ✅ NanoBot |
| 輕量個人助理，簡單部署 | ✅ NanoBot |
| 最多通訊平台 (WhatsApp/Telegram/Discord/Signal/iMessage/Teams/LINE/WeChat...) | ✅ OpenClaw |
| macOS/iOS/Android 原生 App + Voice Wake | ✅ OpenClaw |
| iMessage 整合 | ✅ OpenClaw |
| 向量長記憶 + 多階段評分 | ✅ OpenClaw |
| 多 AI 模型協作 (Claude + Codex + Gemini) | ✅ OpenClaw |
| Live Canvas + Voice Wake + Talk Mode | ✅ OpenClaw |
| 安全審計 + Tool Result Guard + seccomp/AppArmor | ✅ OpenClaw |
| Multi-Agent Routing + ACP 協議 | ✅ OpenClaw |
| 生產環境，企業級需求 | ✅ OpenClaw |
| 從 OpenClaw 遷移到開源替代 | ✅ NanoBot（代碼量少 99%，易於理解和修改） |
