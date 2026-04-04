# Moltis vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：Moltis (moltis-org/moltis), OpenClaw v2026.3.23+ | Repo: moltis-org/moltis ⭐ 2,479 | 🦀 Rust

---

## 1️⃣ 核心定位

| 項目 | Moltis | OpenClaw |
|------|--------|----------|
| **開發者** | moltis-org（開源社群） | 開源社群 |
| **設計理念** | **A secure persistent personal agent server** — One binary, sandboxed, secure, auditable | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **核心差異** | **Rust 單一可執行檔 + WASM 沙盒** — 強調安全性、審計性、持久化 | **Gateway 集中管理 + Plugin 架構** — 最大通訊覆蓋 + ACP 委派 |
| **語言** | Rust | TypeScript (Node.js) |
| **Stars** | 2,479 ⭐ | ~57,000+ ⭐ |
| **Forks** | 291 | ~14,000+ |
| **License** | MIT | GPL-3.0 |
| **Open Issues** | 83 | 高 |
| **安裝方式** | 單一 binary 下載 | npm / 多種安裝方式 |
| **主要使用場景** | 安全優先的持久化 AI 伺服器、審計合規場景、單一部署 | 多平台通訊、多 Agent 協作、開發自動化 |
| **官網** | https://moltis.org |  |

---

## 2️⃣ 架構比較

### Moltis 架構

#### Single Binary 設計
- **Rust 單一可執行檔**：無依賴、無執行時環境需求，一個檔案即可運行
- **內建功能**：Voice、Memory、MCP Tools、Multi-channel access 全部打包在同一個 binary 中
- **持久化伺服器**：設計為持續運行的 agent server，而非一次性 CLI 工具

#### 技術棧與專案結構（推斷，基於 Rust 生態）
| 目錄/模組 | 功能 |
|-----------|------|
| `src/agent/` | Agent 核心邏輯與任務處理 |
| `src/sandbox/` | WASM 沙盒引擎與隔離機制 |
| `src/memory/` | 持久化記憶系統 |
| `src/voice/` | 語音處理（STT/TTS） |
| `src/channels/` | 多通道接入適配器 |
| `src/mcp/` | MCP 工具介面實現 |

### OpenClaw 架構

#### Gateway + Agent Runtime 分離
- **Gateway**：WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**：Pi embedded runner (`runEmbeddedPiAgent`)
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**：channels, models, tools, skills, speech 可 plugin

### 對比總結

| 項目 | Moltis | OpenClaw |
|------|--------|----------|
| **核心架構** | Single Binary — 所有功能內建在一個 Rust 可執行檔中 | Gateway + Agent Runtime 分離 — 模組化設計 |
| **部署模型** | 單一 binary，零依賴部署 | Node.js runtime + npm 包管理 |
| **沙盒機制** | ✅ WASM sandbox（原生隔離） | Docker(seccomp/AppArmor)/SSH/native |
| **Plugin 系統** | ❌ 功能內建，無 plugin 架構 | ✅ Channels/Models/Tools/Skills 可 plugin |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ✅ 內建語音功能 | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |
| **Device Node Protocol** | ❌ | ✅ Camera/Screen/Location/Notification |
| **審計性** | ✅ 設計可審計（auditable） | ⚠️ 透過 audit.ts |
| **Rust 效能** | ✅ 記憶體安全、零 GC 開銷 | ❌ Node.js V8 runtime |

---

## 3️⃣ LLM Provider 支援

### Moltis
- **Provider 支援**：作為個人 agent server，推斷支援主流 LLM Provider（OpenAI、Anthropic 等）
- **MCP Tools**：使用 Model Context Protocol 標準，天然支援 MCP 兼容的 LLM
- **配置方式**：推斷為配置文件驅動，Rust 生態的型別安全配置
- **優勢**：Rust 型別系統保障的配置正確性，編譯期驗證

### OpenClaw
- **多 Provider**：OpenAI, Anthropic, Google, Z.ai 等多個 provider
- **Auth Profiles**：每個 agent 有自己的 `auth-profiles.json`
- **Fallback chains**：primary → fallbacks（依順序），provider auth failover
- **Custom providers**：`models.json` 可自訂
- **OAuth 支援**：OpenAI Codex, Anthropic setup-token
- **Model allowlist**：`agents.defaults.models`
- **Provider normalization**：lowercase + aliases

### 對比總結

| 項目 | Moltis | OpenClaw |
|------|--------|----------|
| **Provider 數量** | 推斷主流 Provider（OpenAI/Claude 等） | 多 Provider + OAuth |
| **MCP 原生支援** | ✅ MCP Tools 為核心設計 | ❌ 透過 plugin 實現 |
| **Dynamic Switching** | ⚠️ 推斷支援（配置驅動） | ✅ 透過 /model command |
| **Credential Pool** | ⚠️ 推斷單一配置 | ❌ Auth Profile per-agent |
| **Fallback chains** | ⚠️ 未知 | ✅ Primary + fallbacks |
| **Custom endpoint** | ⚠️ 未知 | ✅ Custom providers in models.json |
| **Model allowlist** | ⚠️ 未知 | ✅ `agents.defaults.models` |

---

## 4️⃣ 記憶與學習機制

### Moltis
- **持久化記憶**：內建 Memory 系統作為核心功能之一，設計為持久的個人 agent server
- **Rust 型別安全**：記憶系統受 Rust 型別系統保護，減少 runtime 錯誤
- **推斷能力**：作為 persistent server，應具備跨 session 的記憶保留能力
- **學習機制**：未知具體學習機制，可能透過記憶體檔案實現

### OpenClaw
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Embedding provider selection**：可配置外部 embedding provider
- **Hybrid search**：向量 + FTS（全文搜尋）
- **Context Compaction**：`compactEmbeddedPiSession` 自動壓縮長對話歷史
- **Skills System**：Directory-based skills（SKILL.md + env vars），per-agent vs shared
- **Auto-discovery**：agent 啟動時自動發現可用 skills

### 對比總結

| 項目 | Moltis | OpenClaw |
|------|--------|----------|
| **記憶持久化** | ✅ 持久化記憶（核心功能） | ✅ MemoryIndexManager + Markdown |
| **向量搜尋** | ⚠️ 未知 | ✅ Embedding + hybrid search |
| **FTS 全文搜尋** | ⚠️ 未知 | ✅ FTS (hybrid) |
| **每輪自動注入** | ⚠️ 未知 | ✅ 需 sync triggers |
| **Context 壓縮** | ⚠️ 未知 | ✅ compactEmbeddedPiSession |
| **Skill 系統** | ❌ 內建功能，無技能系統 | ✅ SKILL.md + env vars |
| **Auto-discovery** | ⚠️ 未知 | ✅ agent 啟動時自動發現 |
| **型別安全** | ✅ Rust 編譯期保障 | ❌ JavaScript runtime |

---

## 5️⃣ 工具與擴展

### Moltis
- **MCP Tools**：**Model Context Protocol** 工具為核心功能 — 標準化、可互操作的 AI 工具格式
- **內建功能**：Voice、Memory、Multi-channel access 全部內建，無需額外安裝
- **WASM 擴展**：WASM 沙盒不僅用於安全，也可作為工具擴展機制
- **單一部署**：所有工具與功能在同一個 binary 中，無外部依賴

### OpenClaw
- **內建工具**：exec/process, code_execution, browser, web_search, read/write/edit, cron, image_generate 等
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation 等
- **Plugin tools**：Lobster(typed workflow), LLM Task, Diffs, OpenProse
- **CronService**：持久化 job storage + session scopes + heartbeat
- **TTS/STT**：OpenAI TTS, ElevenLabs streaming, Whisper transcription
- **ACP & Sub-Agents**：ACP 協議委派外部 CLI，sessions_spawn/yield

### 對比總結

| 項目 | Moltis | OpenClaw |
|------|--------|----------|
| **工具標準** | ✅ MCP（Model Context Protocol） | 自訂 Tool 系統 + Plugin |
| **工具數量** | 推斷有限（MCP 驅動） | 12+ 內建 + plugins |
| **Voice** | ✅ 內建 | ✅ OpenAI/11Labs TTS + Whisper STT |
| **Cron** | ⚠️ 未知 | ✅ CronService + session scopes |
| **圖片生成** | ⚠️ 未知 | ✅ image_generate(openai/fal/google) |
| **Sub-agents** | ⚠️ 未知 | ✅ ACP + ACPX(external CLI) |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Agent-to-Agent** | ❌ | ✅ agentToAgent tool (可啟用) |
| **擴展機制** | WASM sandbox / MCP | Plugin system |

---

## 6️⃣ 多 Agent 架構

### Moltis
- **單一 Agent 伺服器**：設計為單一個人的持久化 agent server，非多 agent 架構
- **隔離機制**：WASM sandbox 提供執行隔離，但主要是安全隔離而非多 agent 隔離

### OpenClaw
- **ACP (Agent Control Protocol)**：parent-child session 隔離 + delivery dispatch
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派給 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **agentToAgent**：agent 之間互相發送訊息（可選）
- **Cross-agent QMD**：Agent A 搜尋 Agent B 的會話

### 對比總結

| 項目 | Moltis | OpenClaw |
|------|--------|----------|
| **多 Agent 支援** | ❌ 單一 agent server | ✅ ACP + ACPX 多 agent 架構 |
| **子任務委派** | ❌ | ✅ sessions_spawn/yield + ACPX |
| **外部 CLI 委派** | ❌ | ✅ Claude/Codex/Gemini/Kiro/Cursor |
| **Agent Registry** | ❌ | ✅ ACP sub-agent registry |
| **Agent 通訊** | ❌ | ✅ agentToAgent tool |
| **Cross-agent Search** | ❌ | ✅ Cross-agent QMD search |

---

## 7️⃣ 安全性與沙盒

### Moltis（核心賣點）

#### WASM 沙盒
- **WebAssembly 隔離**：所有工具/程式碼執行在 WASM sandbox 中
- **記憶體安全**：Rust + WASM 雙重安全保障 — 無記憶體溢出漏洞
- **可審計（Auditable）**：WASM 位元碼可靜態分析、驗證，行為完全可追蹤
- **系統隔離**：WASM 天然隔離檔案系統、網路、系統呼叫

#### Rust 安全特性
- **零不安全 Rust**：若專案堅持 `#![forbid(unsafe_code)]`，則無記憶體安全風險
- **編譯期保障**：借用檢查器、生命週期分析在編譯期排除大量 bug
- **型別安全**：強型別系統防止資料型別錯誤
- **單一 Binary**：無依賴供應鏈攻擊風險（vs npm 生態的 node_modules 風險）

### OpenClaw
- **Sandbox 後端**：Docker / SSH / 原生 OS 隔離
- **seccomp / AppArmor**：進程級安全策略
- **Workspace 隔離**：獨立 workspace root，per-session workspaces
- **Tool Policy**：`tools.allow` / `tools.deny` / `tools.profile` / `tools.byProvider`
- **Security Audit**：`audit.ts` / `audit-fs.ts` / `audit-extra.ts` / `fix.ts`
- **Zod Schema 驗證**：嚴格的 config 驗證，未知 key 直接拒絕啟動
- **Capability Model**：定義 channel/agent 允許的操作

### 對比總結

| 項目 | Moltis | OpenClaw |
|------|--------|----------|
| **Sandbox 機制** | ✅ **WASM sandbox（輕量級、快速）** | Docker(seccomp/AppArmor) |
| **記憶體安全** | ✅ Rust + WASM 雙重保障 | ❌ Node.js (V8) |
| **可審計性** | ✅ WASM 位元碼可靜態分析 | ⚠️ audit.ts 腳本 |
| **依賴風險** | ✅ 單一 binary，無供應鏈風險 | ⚠️ npm 依賴鏈 |
| **Tool Policy** | ⚠️ 未知 | ✅ allow/deny/profile/byProvider |
| **DM Policy** | ⚠️ 未知 | ✅ pairing/allowlist/open/disabled |
| **Config Validation** | ✅ Rust 型別安全 | ✅ Zod Schema Validation |
| **Secret Management** | ⚠️ 未知 | ✅ SecretRef + encrypted auth-profile |

---

## 8️⃣ 平台與 Gateway

### Moltis
- **Multi-channel access built-in**：多種通道內建於單一 binary
- **具體通道**：推斷支援主流 IM 平台（Discord, Telegram 等）及 WebSocket/HTTP
- **伺服器模式**：設計為持續運行的伺服器，而非桌面應用程式

### OpenClaw
- **25+ 平台**：Discord, Telegram, Slack, WhatsApp, Signal, Matrix, Mattermost, Feishu, Google Chat, iMessage, IRC, Teams, LINE, WeChat 等
- **原生 App**：macOS (菜單列控制平面), iOS Node, Android Node
- **特色功能**：Live Canvas, Voice Wake, Talk Mode, WebChat, Webhook

### 對比總結

| 項目 | Moltis | OpenClaw |
|------|--------|----------|
| **平台總數** | 推斷 5-10 個（內建於 binary） | ~25+（含原生 App） |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ✅ 內建語音 | ✅ always-on + Talk Mode |
| **Live Canvas** | ❌ | ✅ A2UI |
| **Web UI** | ⚠️ 未知 | ✅ Gateway UI + WebChat |
| **部署模式** | 伺服器模式 | 伺服器 + 桌面 + 行動端 |

---

## 9️⃣ 架構流程圖

### Moltis 架構

```
┌─────────────────────────────────────────┐
│           Moltis Single Binary           │
│                                          │
│  ┌──────────┐  ┌──────────┐  ┌────────┐ │
│  │ Channels │  │  Agent   │  │ Memory │ │
│  │ (Built-  │──│  Core    │──│Persist │ │
│  │  in)     │  │(Rust)    │  │        │ │
│  └──────────┘  └────┬─────┘  └────────┘ │
│                     │                    │
│              ┌──────┴──────┐             │
│              │             │             │
│        ┌─────┴────┐  ┌────┴────┐        │
│        │ Voice STT │  │  MCP    │        │
│        │   / TTS   │  │ Tools   │        │
│        └───────────┘  └────┬────┘        │
│                            │             │
│                    ┌───────┴────────┐    │
│                    │  WASM Sandbox  │    │
│                    │  (工具執行隔離) │    │
│                    └────────────────┘    │
└─────────────────────────────────────────┘
         │                      │
         ▼                      ▼
    用戶通訊平台            LLM Provider
```

### OpenClaw 架構

```
┌─────────┐  ┌─────────┐  ┌─────────┐
│ Discord  │  │Telegram │  │  Slack  │  ... 25+ 平台
└────┬─────┘  └────┬────┘  └────┬────┘
     │              │             │
┌────┴──────────────┴─────────────┴──────┐
│            Gateway (WebSocket)          │
│  Multi-agent routing + Session Mgmt     │
└────────────────┬───────────────────────┘
                 │
    ┌────────────┴────────────┐
    │                         │
    ▼                         ▼
┌──────────────┐    ┌──────────────────┐
│ Agent Runtime│    │ ACP Sub-agents   │
│ (Pi runner)  │    │ sessions_spawn    │
└──────┬───────┘    │ + ACPX Harness   │
       │            └──────────────────┘
       │
┌──────┴───────────────────────────┐
│ Tools: exec/browser/web/cron    │
│ Skills: SKILL.md auto-discovery │
│ Memory: MemoryIndexManager      │
│ Docker Sandbox (seccomp)        │
└─────────────────────────────────┘
```

---

## 🔟 總結對比表

| 維度 | Moltis | OpenClaw |
|------|--------|----------|
| **定位** | 安全持久化個人 Agent 伺服器 | 多平台通訊 + 多 Agent 協作 |
| **語言** | 🦀 Rust | TypeScript (Node.js) |
| **部署** | 單一 Binary（零依賴） | npm package + Node.js runtime |
| **核心特色** | WASM 沙盒 + 安全審計 | ACP 委派 + 最大平台覆蓋 |
| **工具標準** | MCP (Model Context Protocol) | 自訂 Tool + Plugin 系統 |
| **安全等級** | ⭐⭐⭐⭐⭐ Rust + WASM 雙重保障 | ⭐⭐⭐⭐ Docker + seccomp |
| **記憶體安全** | ✅ 編譯期保障 | ❌ V8 垃圾回收 |
| **依賴風險** | ✅ 零供應鏈風險 | ⚠️ npm 依賴鏈 |
| **多 Agent** | ❌ 單一 Agent | ✅ ACP + ACPX 完整支援 |
| **平台覆蓋** | ⭐⭐⭐ Multi-channel built-in | ⭐⭐⭐⭐⭐ 25+ 平台 + 原生 App |
| **社群規模** | 2,479 ⭐（成長中） | ~57,000+ ⭐（成熟） |
| **License** | MIT（寬鬆） | GPL-3.0（Copyleft） |
| **適合場景** | 安全審計、單一部署、Rust 生態 | 多平台、多 Agent、開發自動化 |

---

## 11️⃣ 選哪個？

### 選擇 Moltis 如果你...
- 🛡️ **安全性是第一優先** — Rust + WASM 提供業界頂級的安全保障
- 📦 **喜歡單一 Binary 部署** — 一個檔案，無需安裝任何依賴
- 🔍 **需要審計能力** — WASM 位元碼可靜態驗證，行為完全透明
- 🏠 **只需要個人 Agent 伺服器** — 不需要多 Agent 協作
- 🦀 **偏好 Rust 生態** — 記憶體安全、高性能、零 GC 開銷

### 選擇 OpenClaw 如果你...
- 📱 **需要最大平台覆蓋** — 25+ 平台 + iOS/Android 原生 App
- 🤝 **需要多 Agent 協作** — ACP + ACPX 支援外部 CLI 委派
- 🔌 **需要 Plugin 生態** — 可擴展的工具和技能系統
- 🎨 **需要 Live Canvas + Voice Wake** — 交互式 UI + always-on 語音
- 🏗️ **用於開發自動化** — 最成熟的 coding assistant 整合

### 總結

Moltis 和 OpenClaw 代表了兩種完全不同的設計哲學：

- **Moltis** 走「安全優先、單一部署」路線，用 Rust + WASM 構建一個極致安全、可審計的個人 agent 伺服器。適合對安全性要求高、偏好簡化部署的用戶。
- **OpenClaw** 走「生態優先、最大覆蓋」路線，用 TypeScript 構建了一個龐大的通訊平台覆蓋 + 多 Agent 協作架構。適合需要多平台整合、進階 agent 委派的使用場景。
