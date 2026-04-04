# RivonClaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：RivonClaw (gaoyangz77/rivonclaw), OpenClaw v2026.3.23+ | Repo: gaoyangz77/rivonclaw ⭐ 252 | English + 中文

---

## 1️⃣ 核心定位

| 項目 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **開發者** | gaoyangz77（個人開發者） | 開源社群 |
| **設計理念** | **Built ON TOP of OpenClaw** — Easy-mode runtime + UI layer，讓 AI Agent 自行進化 | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **核心差異** | **OpenClaw 的上層封裝** — 無需手動配置 skills/workflows，Desktop app + 系統托盤 + 本地 Web 面板，Agent 可自行進化 | **底層框架** — 需要手動配置 skills、workflows，提供最大的靈活性和控制力 |
| **語言** | TypeScript | TypeScript (Node.js) |
| **Stars** | 252 ⭐ | ~57,000+ ⭐ |
| **Forks** | 56 | ~14,000+ |
| **Language** | English + 中文 | English |
| **主要使用場景** | 桌面端 AI 助理、零配置自動化、Agent 自主進化 | 多平台通訊、多 Agent 協作、開發自動化 |
| **依賴關係** | **依賴 OpenClaw（Wrapper）** | 獨立完整框架 |

---

## 2️⃣ 架構比較

### RivonClaw 架構

#### OpenClaw 之上層
- **Runtime + UI Layer**：在 OpenClaw 之上新增了一個易用層（easy-mode）
- **Desktop Application**：提供桌面應用程式體驗
- **系統托盤**：常駐系統托盤，隨時可用
- **本地 Web 面板**：提供瀏覽器介面管理 Agent

#### 核心特色（差異化價值）
- **零配置**：無需手動配置 skills/workflows
- **自然語言規則**：用自然語言定義規則，Agent 自動理解和執行
- **反饋演進**：Agent 根據用戶反饋自行進化和調整行為
- **雙語支援**：原生支援 English 和 中文

#### 專案結構（推斷，基於 OpenClaw wrapper 模式）
| 目錄/模組 | 功能 |
|-----------|------|
| `src/ui/` | 桌面 UI + 系統托盤 |
| `src/web/` | 本地 Web 面板前端 |
| `src/evolution/` | Agent 自行進化機制 |
| `src/rules/` | 自然語言規則引擎 |
| `wrapper/` | OpenClaw 封裝層 |

### OpenClaw 架構

#### Gateway + Agent Runtime 分離
- **Gateway**：WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**：Pi embedded runner (`runEmbeddedPiAgent`)
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**：channels, models, tools, skills, speech 可 plugin

#### 平台架構特點
- **Node 系統**：macOS/iOS/Android 原生 app 作為 Device Node
- **Device Node Protocol**：WebSocket-based RPC
- **Live Canvas (A2UI)**：Agent 可渲染交互式 UI canvas
- **Voice Wake + Talk Mode**：always-on 語音喚醒

### 對比總結

| 項目 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **核心架構** | OpenClaw Wrapper + Easy-mode UI | Gateway + Agent Runtime 分離 |
| **依賴關係** | ⚠️ **依賴 OpenClaw** | 獨立完整框架 |
| **桌面 UI** | ✅ 桌面 app + 系統托盤 | ✅ macOS 菜單列 App |
| **Web 面板** | ✅ 本地 Web UI | ✅ Gateway UI + WebChat (localhost:18789) |
| **插件系統** | ❌ 繼承 OpenClaw 的插件系統 | ✅ Channels/Models/Tools/Skills |
| **Voice Wake** | ❌ 未知 | ✅ always-on + Talk Mode |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |
| **進化能力** | ✅ Agent 自行進化 | ❌ 需手動配置 |
| **語言支援** | ✅ English + 中文 | English（社群翻譯可選） |

---

## 3️⃣ LLM Provider 支援

### RivonClaw
- **繼承 OpenClaw**：由於是 OpenClaw 的 wrapper，推斷使用 OpenClaw 的 provider 配置
- **簡化管理**：UI 層可能簡化了 provider 配置流程
- **雙語介面**：中文用戶可以更直觀地配置 LLM 設定

### OpenClaw
- **多 Provider**：OpenAI, Anthropic, Google, Z.ai 等
- **Auth Profiles**：每個 agent 有自己的 `auth-profiles.json`
- **Fallback chains**：primary → fallbacks
- **Custom providers**：`models.json` 可自訂
- **OAuth 支援**：OpenAI Codex, Anthropic setup-token
- **Model allowlist**：`agents.defaults.models`

### 對比總結

| 項目 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **Provider 支援** | ✅ 繼承 OpenClaw | 多 Provider + OAuth |
| **配置體驗** | ✅ GUI 配置（推斷） | 檔案編輯 + CLI |
| **Fallback chains** | ✅ 繼承 | ✅ Primary + fallbacks |
| **Custom endpoint** | ✅ 繼承 | ✅ Custom providers |
| **中文配置體驗** | ✅ 原生中文支援 | ❌ 英文介面 |

---

## 4️⃣ 記憶與學習機制

### RivonClaw
- **繼承 OpenClaw Memory**：使用 OpenClaw 的 MemoryIndexManager
- **反饋學習機制**：Agent 根據用戶反饋自行演進，這是 RivonClaw 的核心差異化功能
- **自然語言規則**：用戶可以用自然語言定義規則，而非編寫 skill 檔案
- **持續進化**：Agent 行為會隨著互動不斷調整和優化

### OpenClaw
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Embedding provider selection**：可配置外部 embedding provider
- **Hybrid search**：向量 + FTS（全文搜尋）
- **Context Compaction**：`compactEmbeddedPiSession` 自動壓縮長對話歷史
- **Skills System**：SKILL.md + env vars，per-agent vs shared
- **Auto-discovery**：agent 啟動時自動發現可用 skills

### 對比總結

| 項目 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **記憶持久化** | ✅ 繼承 OpenClaw | ✅ MemoryIndexManager + Markdown |
| **向量搜尋** | ✅ 繼承 | ✅ Embedding + hybrid search |
| **反饋學習** | ✅ **核心特色** — 自行進化 | ❌ 需手動調整 skill |
| **自然語言規則** | ✅ **核心特色** | ❌ 需編寫 SKILL.md |
| **Context 壓縮** | ✅ 繼承 | ✅ compactEmbeddedPiSession |
| **Skill 系統** | ✅ 繼承（但可隱藏） | ✅ SKILL.md + env vars |

---

## 5️⃣ 工具與擴展

### RivonClaw
- **繼承 OpenClaw 工具**：所有 OpenClaw 工具自動可用
- **自然語言規則引擎**：用自然語言替代手動 skill 配置
- **UI 驅動擴展**：透過 GUI 而非文字配置擴展功能
- **無需技能配置**：Agent 自行管理 skills/workflows

### OpenClaw
- **內建工具**：exec/process, code_execution, browser, web_search, read/write/edit, cron, image_generate 等
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation 等
- **Plugin tools**：Lobster(typed workflow), LLM Task, Diffs, OpenProse
- **CronService**：持久化 job storage + session scopes + heartbeat
- **TTS/STT**：OpenAI TTS, ElevenLabs streaming, Whisper transcription

### 對比總結

| 項目 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **工具集** | ✅ 全繼承 | 12+ 內建 + plugins |
| **配置方式** | 🎯 GUI + 自然語言 | 檔案編輯 + CLI |
| **Cron** | ✅ 繼承 | ✅ CronService |
| **TTS/STT** | ✅ 繼承 | ✅ OpenAI/11Labs TTS + Whisper |
| **Sub-agents** | ✅ 繼承 ACP | ✅ ACP + ACPX |
| **Workflow DSL** | ✅ 繼承 Lobster | ✅ Lobster |

---

## 6️⃣ 多 Agent 架構

### RivonClaw
- **繼承 OpenClaw ACP**：由於是 wrapper，ACP 功能理論上可用
- **UI 層管理**：可能提供 UI 來查看和管理多個 agent
- **隱藏複雜度**：對用戶隱藏多 agent 的複雜配置

### OpenClaw
- **ACP (Agent Control Protocol)**：parent-child session 隔離 + delivery dispatch
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派給 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **agentToAgent**：agent 之間互相發送訊息
- **Cross-agent QMD**：Agent A 搜尋 Agent B 的會話

### 對比總結

| 項目 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **多 Agent 支援** | ✅ 理論繼承 | ✅ ACP + ACPX 多 agent 架構 |
| **子任務委派** | ✅ 繼承 | ✅ sessions_spawn/yield + ACPX |
| **UI 管理** | ✅ 可能提供 GUI 管理 | ❌ CLI 為主 |
| **Agent Registry** | ✅ 繼承 | ✅ ACP sub-agent registry |
| **Agent 通訊** | ✅ 繼承 | ✅ agentToAgent tool |

---

## 7️⃣ 安全性與沙盒

### RivonClaw
- **繼承 OpenClaw 安全機制**：使用 OpenClaw 的 Docker sandbox、seccomp/AppArmor 等
- **UI 層抽象**：安全配置透過 GUI 進行，對非技術用戶更友善
- **額外風險**：wrapper 層本身可能引入新的安全考量
- **實驗性質**：較小的專案，安全審計較少

### OpenClaw
- **Sandbox 後端**：Docker / SSH / 原生 OS 隔離
- **seccomp / AppArmor**：進程級安全策略
- **Workspace 隔離**：獨立 workspace root，per-session workspaces
- **Tool Policy**：`tools.allow` / `tools.deny` / `tools.profile` / `tools.byProvider`
- **Security Audit**：`audit.ts` / `audit-fs.ts` / `audit-extra.ts` / `fix.ts`
- **Zod Schema 驗證**：嚴格的 config 驗證
- **Capability Model**：定義 channel/agent 允許的操作

### 對比總結

| 項目 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **Sandbox 機制** | ✅ 繼承 Docker/seccomp | Docker(seccomp/AppArmor) |
| **Tool Policy** | ✅ 繼承 | ✅ allow/deny/profile/byProvider |
| **DM Policy** | ✅ 繼承 | ✅ pairing/allowlist/open/disabled |
| **Config Validation** | ✅ 繼承 Zod | ✅ Zod Schema |
| **Wrapper 風險** | ⚠️ 額外抽象層 | N/A |
| **專案審計** | ⚠️ 較少（新專案） | ✅ 成熟社群審計 |

---

## 8️⃣ 平台與 Gateway

### RivonClaw
- **桌面應用程式**：專屬桌面 app 體驗
- **系統托盤**：常駐系統托盤，快速存取
- **本地 Web 面板**：瀏覽器管理介面
- **專注桌面體驗**：不覆蓋 OpenClaw 的通訊平台能力，但提供更好的桌面 UI

### OpenClaw
- **25+ 平台**：Discord, Telegram, Slack, WhatsApp, Signal, Matrix, Mattermost, Feishu, Google Chat, iMessage, IRC, Teams, LINE, WeChat 等
- **原生 App**：macOS (菜單列控制平面), iOS Node, Android Node
- **特色功能**：Live Canvas, Voice Wake, Talk Mode, WebChat, Webhook

### 對比總結

| 項目 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **桌面 App** | ✅ 專屬桌面 app | ✅ macOS 菜單列 App |
| **系統托盤** | ✅ | ✅（macOS 菜單列） |
| **Web 面板** | ✅ 本地 Web UI | ✅ Gateway UI + WebChat |
| **通訊平台** | ✅ 繼承（25+） | 25+ |
| **原生 App** | ❌ 僅桌面 | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ always-on |

---

## 9️⃣ 架構流程圖

### RivonClaw 架構（OpenClaw Wrapper）

```
┌────────────────────────────────────────────┐
│              RivonClaw UI Layer             │
│                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │ Desktop  │  │ System   │  │ Web      │  │
│  │   App    │  │  Tray    │  │  Panel   │  │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  │
│       │              │              │        │
│  ┌────┴──────────────┴──────────────┴─────┐ │
│  │  Easy-mode Runtime                      │ │
│  │  ┌────────────┐  ┌──────────────────┐   │ │
│  │  │ 自然語言   │  │ Agent 自行       │   │ │
│  │  │ 規則引擎   │  │ 進化機制          │   │ │
│  │  └────────────┘  └──────────────────┘   │ │
│  └────────────────┬────────────────────────┘ │
│                   │ Wrapper 封裝               │
└───────────────────┼──────────────────────────┘
                    │
    ┌───────────────┴───────────────────┐
    │         OpenClaw Gateway           │
    │  WebSocket + Session + Routing     │
    └───────────────┬───────────────────┘
                    │
    ┌───────────────┴───────────────────┐
    │     OpenClaw Agent Runtime        │
    │  Tools + Skills + Memory + ACP    │
    └───────────────────────────────────┘
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

| 維度 | RivonClaw | OpenClaw |
|------|-----------|----------|
| **定位** | OpenClaw 的 Easy-mode UI 層 | 完整的個人 AI Agent 框架 |
| **語言** | TypeScript (English + 中文) | TypeScript (English) |
| **架構關係** | OpenClaw Wrapper（依賴） | 獨立完整框架 |
| **核心特色** | 桌面 app + 系統托盤 + 自行進化 | ACP 委派 + 最大平台覆蓋 |
| **Zero Config** | ✅ 無需手動配置 skills | ❌ 需手動配置 |
| **自然語言規則** | ✅ 核心差異化功能 | ❌ 需編寫 SKILL.md |
| **反饋演進** | ✅ Agent 自行進化 | ❌ 靜態配置 |
| **工具集** | ✅ 全繼承 OpenClaw | 12+ 內建 + plugins |
| **多 Agent** | ✅ 繼承 ACP | ✅ ACP + ACPX |
| **平台覆蓋** | ✅ 繼承 25+ | 25+ + 原生 App |
| **社群規模** | 252 ⭐（新興） | ~57,000+ ⭐（成熟） |
| **適合用戶** | 想要「開箱即用」體驗的用戶 | 想要完全控制的進階用戶 |

---

## 11️⃣ 選哪個？

### 選擇 RivonClaw 如果你...
- 🖥️ **想要桌面應用體驗** — 桌面 app + 系統托盤 + Web 面板，即開即用
- 🚫 **不想手動配置** — 零配置，無需寫 SKILL.md 或 workflows
- 🗣️ **偏好自然語言操作** — 用自然語言定義規則，Agent 自動理解
- 🌱 **想要 Agent 自行進化** — Agent 根據反饋自動調整行為
- 🇨🇳 **需要中文支援** — 原生 English + 中文雙語體驗
- 🪆 **已經是 OpenClaw 用戶** — 在 OpenClaw 之上獲得更好的 UI 層

### 選擇 OpenClaw 如果你...
- 🔧 **想要完全控制** — 手動配置每一個 skill、workflow、tool
- 📱 **需要 25+ 平台覆蓋** — 不只是桌面，還有行動端和通訊平台
- 🎨 **需要進階功能** — Live Canvas, Voice Wake, Talk Mode
- 🏗️ **用於開發自動化** — 最成熟的 coding assistant 整合
- 🤝 **需要多 Agent 協作** — ACP + ACPX 完整支援
- 📦 **不想有額外依賴** — 獨立的完整框架，不依賴其他專案

### 總結

RivonClaw 和 OpenClaw 的關係不是競爭，而是**互補與增強**：

- **RivonClaw 是 OpenClaw 的 "Easy Mode"** — 它在 OpenClaw 這個強大框架之上建造了一個用戶友好的入口。對於不想碰配置檔、不想寫 SKILL.md 的用戶來說，RivonClaw 是一個理想的入場券。
- **核心價值主張**：零配置 + 自然語言規則 + Agent 自行進化，這三個差异化功能如果做得好，可以極大地降低 AI Agent 的使用門檻。
- **風險**：作為 OpenClaw 的 wrapper，RivonClaw 與 OpenClaw 的開發進度緊密綁定。如果 OpenClaw 的 API 發生重大變更，RivonClaw 需要同步更新。同時，wrapper 層也可能成為瓶頸。

如果你喜歡 OpenClaw 的能力但嫌配置太複雜，RivonClaw 值得一試；如果你追求極致控制和靈活性，直接使用 OpenClaw 更好。
