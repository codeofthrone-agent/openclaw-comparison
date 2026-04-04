# ZeptoClaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：ZeptoClaw (qhkm/zeptoclaw), OpenClaw v2026.3.23+ | Repo: qhkm/zeptoclaw ⭐ 556 | 🦀 Rust

---

## 1️⃣ 核心定位

| 項目 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **開發者** | qhkm（個人開發者，Claw 家族變體） | 開源社群 |
| **設計理念** | **"Final form of claw family (Wannabe)"** — Ultra-lightweight personal AI assistant | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **核心差異** | **極致輕量化** — Rust 編寫的微型 AI 助理，吉祥物 Zippy | **完整生態系統** — 最大通訊覆蓋 + ACP 委派 + Plugin 架構 |
| **語言** | Rust | TypeScript (Node.js) |
| **Stars** | 556 ⭐ | ~57,000+ ⭐ |
| **Forks** | 78 | ~14,000+ |
| **License** | Apache-2.0 | GPL-3.0 |
| **Open Issues** | 35 | 高 |
| **狀態** | ⚠️ 早期實驗專案 | ✅ 成熟穩定 |
| **吉祥物** | ✅ Zippy | ❌ |
| **主要使用場景** | 個人快速 AI 助理、輕量級本地運行、學習 Rust + AI | 多平台通訊、多 Agent 協作、開發自動化 |

---

## 2️⃣ 架構比較

### ZeptoClaw 架構

#### Ultra-lightweight 設計
- **Rust 輕量核心**：使用 Rust 編寫的超輕量 AI 助理框架
- **長期記憶**：內建 `longterm_memory` 作為核心功能
- **吉祥物系統**：Zippy 吉祥物為專案特色之一
- **實驗性質**：作為 "claw family" 的終極形態（自稱），定位為 Wannabe — 仍在早期開發階段

#### 專案結構（推斷，基於輕量 Rust 專案模式）
| 目錄/模組 | 功能 |
|-----------|------|
| `src/agent/` | 輕量 Agent 核心 |
| `src/memory/` | `longterm_memory` 長期記憶系統 |
| `src/zippy/` | 吉祥物 Zippy 相關功能 |
| `src/config/` | 最小化配置管理 |

### OpenClaw 架構

#### Gateway + Agent Runtime 分離
- **Gateway**：WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**：Pi embedded runner (`runEmbeddedPiAgent`)
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **Plugin architecture**：channels, models, tools, skills, speech 可 plugin

#### 平台架構特點
- **Node 系統**：macOS/iOS/Android 原生 app 作為 Device Node
- **Device Node Protocol**：WebSocket-based RPC，暴露 camera/screen/location/notification
- **Live Canvas (A2UI)**：Agent 可渲染交互式 UI canvas
- **Voice Wake + Talk Mode**：always-on 語音喚醒

### 對比總結

| 項目 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **核心架構** | Rust 輕量單體 — 最小化設計 | Gateway + Agent Runtime 分離 — 企業級架構 |
| **部署模型** | Rust binary — 輕量部署 | Node.js runtime + npm 包管理 |
| **Plugin 系統** | ❌ 實驗中，功能內建 | ✅ Channels/Models/Tools/Skills 可 plugin |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ macOS/iOS/Android always-on |
| **Live Canvas** | ❌ | ✅ A2UI 交互式 UI |
| **Device Node** | ❌ | ✅ Camera/Screen/Location |
| **成熟度** | ⚠️ 早期實驗階段 | ✅ 穩定生產就緒 |
| **吉祥物** | ✅ Zippy（特色） | ❌ |

---

## 3️⃣ LLM Provider 支援

### ZeptoClaw
- **Provider 支援**：作為輕量 AI 助理，推斷支援主流 LLM Provider
- **配置方式**：推極簡配置（符合 ultra-lightweight 定位）
- **優勢**：Rust 型別安全，配置錯誤在編譯期可部分檢測

### OpenClaw
- **多 Provider**：OpenAI, Anthropic, Google, Z.ai 等多個 provider
- **Auth Profiles**：每個 agent 有自己的 `auth-profiles.json`
- **Fallback chains**：primary → fallbacks（依順序），provider auth failover
- **Custom providers**：`models.json` 可自訂
- **OAuth 支援**：OpenAI Codex, Anthropic setup-token
- **Model allowlist**：`agents.defaults.models`
- **Provider normalization**：lowercase + aliases

### 對比總結

| 項目 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **Provider 數量** | ⚠️ 推斷有限（早期實驗） | 多 Provider + OAuth |
| **Dynamic Switching** | ⚠️ 未知 | ✅ 透過 /model command |
| **Fallback chains** | ❌ 未知（實驗中） | ✅ Primary + fallbacks |
| **Custom endpoint** | ⚠️ 未知 | ✅ Custom providers in models.json |
| **Model allowlist** | ❌ 未知 | ✅ `agents.defaults.models` |
| **Provider normalization** | ⚠️ 未知 | ✅ lowercase + alias |

---

## 4️⃣ 記憶與學習機制

### ZeptoClaw
- **Long-term Memory**：`longterm_memory` 為專案核心功能之一
- **Rust 型別安全**：記憶系統受 Rust 型別系統保護
- **簡化設計**：作為 ultra-lightweight 專案，記憶機制應為最小化實現
- **推斷能力**：可能使用檔案或嵌入式資料庫（如 SQLite）持久化

### OpenClaw
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Embedding provider selection**：可配置外部 embedding provider
- **Hybrid search**：向量 + FTS（全文搜尋）
- **Context Compaction**：`compactEmbeddedPiSession` 自動壓縮長對話歷史
- **Skills System**：SKILL.md + env vars，per-agent vs shared
- **Auto-discovery**：agent 啟動時自動發現可用 skills

### 對比總結

| 項目 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **記憶持久化** | ✅ `longterm_memory` | ✅ MemoryIndexManager + Markdown |
| **向量搜尋** | ❌ 推斷無（輕量化） | ✅ Embedding + hybrid search |
| **FTS 全文搜尋** | ❌ 推斷無 | ✅ FTS (hybrid) |
| **Context 壓縮** | ❌ 推斷無 | ✅ compactEmbeddedPiSession |
| **Skill 系統** | ❌ | ✅ SKILL.md + env vars |
| **Auto-discovery** | ❌ 推斷無 | ✅ agent 啟動時自動發現 |

---

## 5️⃣ 工具與擴展

### ZeptoClaw
- **最小化工具集**：作為 ultra-lightweight 專案，工具集應極簡
- **Rust 實現**：所有工具以 Rust 編寫，效能高且安全
- **擴展能力有限**：目前處於實驗階段，尚無完整擴展框架

### OpenClaw
- **內建工具**：exec/process, code_execution, browser, web_search, read/write/edit, cron, image_generate 等
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：group:runtime, group:fs, group:sessions, group:memory, group:web, group:ui, group:automation 等
- **Plugin tools**：Lobster(typed workflow), LLM Task, Diffs, OpenProse
- **CronService**：持久化 job storage + session scopes + heartbeat
- **TTS/STT**：OpenAI TTS, ElevenLabs streaming, Whisper transcription
- **ACP & Sub-Agents**：ACP 協議委派外部 CLI

### 對比總結

| 項目 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **工具數量** | ❌ 極少數（實驗中） | 12+ 內建 + plugins |
| **Tool Profiles** | ❌ | ✅ full/coding/messaging/minimal |
| **Cron** | ❌ | ✅ CronService + session scopes |
| **TTS/STT** | ❌ | ✅ OpenAI/11Labs TTS + Whisper STT |
| **圖片生成** | ❌ | ✅ image_generate(openai/fal/google) |
| **Sub-agents** | ❌ | ✅ ACP + ACPX(external CLI) |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Agent-to-Agent** | ❌ | ✅ agentToAgent tool (可選) |

---

## 6️⃣ 多 Agent 架構

### ZeptoClaw
- **單一 Agent 架構**：作為個人 AI 助理，設計為單一 Agent
- **無多 Agent 支援**：實驗專案尚不具備多 Agent 能力

### OpenClaw
- **ACP (Agent Control Protocol)**：parent-child session 隔離 + delivery dispatch
- **sessions_spawn / sessions_yield**：子代理會話管理
- **ACPX Harness**：委派給 Claude Code/Codex/Gemini/Kiro/Cursor CLI
- **Multi-agent routing**：依 channel/account/peer 路由到隔離 agent
- **agentToAgent**：agent 之間互相發送訊息
- **Cross-agent QMD**：Agent A 搜尋 Agent B 的會話

### 對比總結

| 項目 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **多 Agent 支援** | ❌ 單一 Agent | ✅ ACP + ACPX 多 agent 架構 |
| **子任務委派** | ❌ | ✅ sessions_spawn/yield + ACPX |
| **外部 CLI 委派** | ❌ | ✅ Claude/Codex/Gemini/Kiro/Cursor |
| **Agent Registry** | ❌ | ✅ ACP sub-agent registry |
| **Agent 通訊** | ❌ | ✅ agentToAgent tool |

---

## 7️⃣ 安全性與沙盒

### ZeptoClaw
- **Rust 記憶體安全**：自然享有 Rust 的記憶體安全保障
- **零 GC 開銷**：無垃圾回收，無記憶體洩漏風險
- **輕量安全**：作為最小化專案，攻擊面相對較小
- **實驗階段**：安全機製尚未完全開發，**不建議用於生產環境**
- **Apache-2.0 License**：企業友善授權

### OpenClaw
- **Sandbox 後端**：Docker / SSH / 原生 OS 隔離
- **seccomp / AppArmor**：進程級安全策略
- **Workspace 隔離**：獨立 workspace root，per-session workspaces
- **Tool Policy**：`tools.allow` / `tools.deny` / `tools.profile` / `tools.byProvider`
- **Security Audit**：`audit.ts` / `audit-fs.ts` / `audit-extra.ts` / `fix.ts`
- **Zod Schema 驗證**：嚴格的 config 驗證
- **Capability Model**：定義 channel/agent 允許的操作

### 對比總結

| 項目 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **記憶體安全** | ✅ Rust 編譯期保障 | ❌ Node.js (V8) |
| **沙盒機制** | ❌ 尚未實現 | Docker(seccomp/AppArmor) |
| **Tool Policy** | ❌ | ✅ allow/deny/profile/byProvider |
| **DM Policy** | ❌ 實驗中 | ✅ pairing/allowlist/open/disabled |
| **Config Validation** | ⚠️ 基礎驗證（推斷） | ✅ Zod Schema Validation |
| **Security Audit** | ❌ | ✅ audit.ts + fix.ts |
| **攻擊面** | ✅ 小（最小化設計） | ⚠️ 大（功能多） |
| **生產就緒** | ❌ 實驗中 | ✅ 穩定 |

---

## 8️⃣ 平台與 Gateway

### ZeptoClaw
- **CLI/輕量運行**：作為 ultra-lightweight 個人助理，可能僅提供 CLI 介面
- **平台支援**：Rust 編譯為各平台 binary（macOS/Linux/Windows）
- **通訊平台**：目前推斷僅支援基礎通訊方式

### OpenClaw
- **25+ 平台**：Discord, Telegram, Slack, WhatsApp, Signal, Matrix, Mattermost, Feishu, Google Chat, iMessage, IRC, Teams, LINE, WeChat 等
- **原生 App**：macOS (菜單列控制平面), iOS Node, Android Node
- **特色功能**：Live Canvas, Voice Wake, Talk Mode, WebChat, Webhook

### 對比總結

| 項目 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **平台總數** | ❌ 極少數（實驗中） | ~25+（含原生 App） |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ always-on + Talk Mode |
| **Live Canvas** | ❌ | ✅ A2UI |
| **Web UI** | ❌ | ✅ Gateway UI + WebChat |
| **跨平台 Binary** | ✅ Rust 編譯 | ❌ Node.js 依賴 |

---

## 9️⃣ 架構流程圖

### ZeptoClaw 架構（推斷）

```
┌──────────────────────────────────────┐
│         ZeptoClaw                      │
│                                        │
│  ┌──────────┐     ┌────────────────┐   │
│  │  User    │────▶│  Agent Core    │   │
│  │  Input   │     │  (Rust, 輕量)   │   │
│  └──────────┘     │                │   │
│                   │  ┌──────────┐  │   │
│                   │  │ longterm │  │   │
│                   │  │ _memory  │──┘   │
│                   │  └──────────┘       │
│                   │                     │
│                   │  ┌──────────┐       │
│                   │  │  Zippy   │       │
│                   │  │(吉祥物)  │       │
│                   │  └──────────┘       │
│                   │                     │
│                   │  ┌──────────┐       │
│                   │  │  LLM     │       │
│                   │  │ Provider │       │
│                   │  └──────────┘       │
│                   └─────────────────────┘
└──────────────────────────────────────────┘
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

| 維度 | ZeptoClaw | OpenClaw |
|------|-----------|----------|
| **定位** | Ultra-lightweight 個人 AI 助理 | 多平台通訊 + 多 Agent 協作 |
| **語言** | 🦀 Rust | TypeScript (Node.js) |
| **部署** | Rust binary（輕量） | npm package + Node.js runtime |
| **核心特色** | longterm_memory + Zippy 吉祥物 | ACP 委派 + 最大平台覆蓋 |
| **工具與擴展** | ❌ 極少數（實驗中） | 12+ 內建 + plugins |
| **安全等級** | ⭐⭐⭐ Rust 記憶體安全 | ⭐⭐⭐⭐ Docker + seccomp |
| **記憶體安全** | ✅ 編譯期保障 | ❌ V8 垃圾回收 |
| **多 Agent** | ❌ 單一 Agent | ✅ ACP + ACPX 完整支援 |
| **平台覆蓋** | ⭐ 極少數 | ⭐⭐⭐⭐⭐ 25+ 平台 + 原生 App |
| **成熟度** | ⚠️ 早期實驗階段 | ✅ 穩定生產就緒 |
| **社群規模** | 556 ⭐（新興） | ~57,000+ ⭐（成熟） |
| **License** | Apache-2.0（寬鬆） | GPL-3.0（Copyleft） |
| **適合場景** | 學習、玩樂、極簡需求 | 多平台、多 Agent、開發自動化 |

---

## 11️⃣ 選哪個？

### 選擇 ZeptoClaw 如果你...
- 🧪 **喜歡實驗性專案** — 想體驗 "claw family" 的最終形態
- 🦀 **想學 Rust + AI** — 輕量的 Rust AI 助理是好的學習素材
- 🐾 **喜歡吉祥物** — Zippy 吉祥物為專案增添趣味
- 🪶 **需要極端輕量** — 最小化的個人 AI 助理，無冗餘功能
- 📜 **偏好 Apache-2.0** — 企業友善的寬鬆授權

### 選擇 OpenClaw 如果你...
- 📱 **需要多平台通訊** — 25+ 平台 + iOS/Android 原生 App
- 🤝 **需要多 Agent 協作** — ACP + ACPX 支援外部 CLI 委派
- 🔌 **需要完整 Plugin 生態** — 可擴展的工具和技能系統
- 🏗️ **用於生產環境** — 穩定、成熟、有完整安全機制
- 🎨 **需要進階功能** — Live Canvas, Voice Wake, Talk Mode 等

### 總結

ZeptoClaw 和 OpenClaw 處於完全不同的成熟度與定位階層：

- **ZeptoClaw** 是一個有趣的實驗性 Rust 專案，屬於 "claw family" 社群的創意分支。作為 ultra-lightweight 個人助理，它展示了 Rust 在 AI 助理領域的可能性，但目前仍處於早期實驗階段，**不建議用於生產環境**。
- **OpenClaw** 是一個成熟的全功能 AI Agent 框架，擁有最廣的通訊平台覆蓋、完善的 ACP 多 Agent 架構和豐富的工具生態。

ZeptoClaw 更像是一個「Wannabee」 —— 它代表了對終極形態的嚮往，但距離真正的 final form 還有很長的路要走。不過，這個方向（輕量 + Rust）確實值得關注，也許未來的某個版本會帶來驚喜。
