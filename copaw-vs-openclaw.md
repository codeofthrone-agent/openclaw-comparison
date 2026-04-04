# CoPaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：CoPaw (agentscope-ai/CoPaw), OpenClaw v2026.3.23+ | Repo: agentscope-ai/CoPaw ⭐ 14,363

---

## 1️⃣ 核心定位

| 項目 | CoPaw | OpenClaw |
|------|-------|----------|
| **開發者** | AgentScope AI（阿里雲 / 清華 AgentScope 團隊） | 開源社群 |
| **設計理念** | Your Personal AI Assistant — 易安裝、可本地或雲端部署，記憶/個性化完全由你掌控 | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **核心差異** | **Python 生態 + 一鍵安裝**：pip install copaw，個人記憶完全自主控制 | **生態系統深度**：最大通訊平台覆蓋 + ACP 委派架構 |
| **語言** | Python | TypeScript (Node.js) |
| **Stars** | 14,363 ⭐ | ~57,000+ ⭐ |
| **License** | Apache-2.0 | GPL-3.0 |
| **安裝方式** | PyPI (`pip install copaw`) | npm / 多種安裝方式 |
| **主要使用場景** | 個人 AI 助理、隱私優先、易擴展、多聊天 APP | 多平台通訊、多 Agent 協作、預測市場整合 |

---

## 2️⃣ 架構比較

### CoPaw 架構

#### 簡易部署架構
- **Python 核心**：標準 Python 套件結構，pip install 即可運行
- **模組化設計**：支援本地部署或雲端部署
- **個人化控制**：Memory 和 personalization 完全在用戶控制下

#### 專案結構（推斷，Python 生態）
| 目錄/模組 | 功能 |
|-----------|------|
| `copaw/` | 核心 Agent 引擎 |
| `memory/` | 個人記憶系統 |
| `channels/` | 多聊天 APP 適配器 |
| `extensions/` | 擴展插件系統 |
| `config/` | 用戶配置 |

### OpenClaw 架構

#### Gateway + Agent Runtime
- **Gateway**：WebSocket 控制面，管理 channel 連接、session、routing
- **Agent Runtime**：Pi embedded runner (`runEmbeddedPiAgent`)
- **Multi-agent routing**：依 channel/account/peer 路由
- **Plugin architecture**：channels, models, tools, skills 可擴展

### 對比總結

| 項目 | CoPaw | OpenClaw |
|------|-------|----------|
| **核心架構** | Python 單體 + Channel 插件 | Gateway + Agent Runtime 分離 |
| **安裝方式** | ✅ `pip install copaw` (一鍵) | ⚠️ npm + Node.js 環境 |
| **部署選項** | 本地 or 雲端 | 本地 (Gateway 運行) |
| **Plugin 系統** | ✅ Extensions | ✅ Channels/Models/Tools/Skills |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ macOS/iOS/Android |
| **Live Canvas** | ❌ | ✅ A2UI |
| **設備 Node 協議** | ❌ | ✅ Camera/Screen/Location |

---

## 3️⃣ LLM Provider 支援

### CoPaw
- **多種 LLM 支援**：支援主流 LLM Provider（OpenAI, Claude, 國內模型等）
- **Provider 配置**：透過配置文件靈活切換
- **OpenAI 兼容 API**：支援 OpenAI API 格式的各種模型

### OpenClaw
- **多 Provider**：OpenAI, Anthropic, Google, Z.ai 等多個
- **Auth Profiles**：每 agent 獨立 `auth-profiles.json`
- **Fallback chains**：primary → fallbacks
- **Custom providers**：`models.json` 可自訂
- **OAuth 支援**：OpenAI Codex, Anthropic setup-token
- **Model allowlist**：`agents.defaults.models`
- **Provider normalization**：lowercase + aliases

### 對比總結

| 項目 | CoPaw | OpenClaw |
|------|-------|----------|
| **Provider 數量** | ✅ 多家主流 Provider | ✅ 多家主流 + 客製化 |
| **動態切換** | ✅ 配置切換 | ✅ /model command |
| **Fallback chains** | ❌ | ✅ Primary + fallbacks |
| **Custom endpoint** | ✅ | ✅ Custom providers |
| **OAuth 支援** | ❌ | ✅ |
| **Model allowlist** | ❌ | ✅ |
| **Provider 正規化** | ❌ | ✅ lowercase + alias |

---

## 4️⃣ 記憶與學習

### CoPaw
- **完全由你控制的記憶**：個人化記憶系統，所有記憶數據在用戶掌控中
- **個人記憶持久化**：本地存儲，隱私優先
- **個性化學習**：Agent 根據用戶偏好進行個性化
- **記憶自主權**：用戶擁有 100% 記憶控制權

### OpenClaw
- **MemoryIndexManager**：索引 workspace 中的 Markdown 檔案
- **Hybrid search**：向量 + FTS
- **Embedding provider**：可配置外部 embedding provider
- **Context compaction**：`compactEmbeddedPiSession`
- **Per-agent agentDir**：獨立 auth/model registry
- **Skills System**：Directory-based skills, per-agent vs shared

### 對比總結

| 項目 | CoPaw | OpenClaw |
|------|-------|----------|
| **Memory 控制** | ✅ 完全由用戶控制 | ✅ 本地記憶 (Markdown indexed) |
| **向量搜尋** | ⚠️ 未明確說明 | ✅ Embedding + hybrid |
| **FTS 全文搜尋** | ❌ | ✅ |
| **個性化** | ✅ 核心設計原則 | ⚠️ 透過 workspace 檔案 |
| **Context 壓縮** | ❌ | ✅ compactEmbeddedPiSession |
| **Skill 系統** | ❌ | ✅ SKILL.md + env injection |
| **Skills Hub** | ❌ | ✅ ClawHub + npm |
| **隱私優先** | ✅ 核心價值 | ✅ 本地運行 |

---

## 5️⃣ 工具與擴展

### CoPaw
- **易擴展設計**：支持擴展插件以添加新功能
- **Python 生態優勢**：可利用 Python 豐富的第三方庫
- **自定義工具**：用戶可自行開發工具擴展

### OpenClaw
- **內建工具**：exec/process, code_execution, browser, web_search, read/write/edit, message, canvas, cron, image_generate
- **Tool profiles**：full/coding/messaging/minimal
- **Tool groups**：9 個 tool group
- **Plugin tools**：Lobster, LLM Task, Diffs, OpenProse
- **CronService**：持久化 job, session scopes, heartbeat, webhook
- **Image & Media**：image_generate, Whisper, PDF, TTS
- **ACP & Sub-Agents**：ACP 協議 + ACPX Harness

### 對比總結

| 項目 | CoPaw | OpenClaw |
|------|-------|----------|
| **內建工具** | ⚠️ Python 生態為主 | ✅ 12+ 內建 + plugins |
| **擴展方式** | Python 擴展 | Plugin SDK |
| **圖片生成** | ❌ | ✅ |
| **TTS/STT** | ❌ | ✅ OpenAI/11Labs + Whisper |
| **Sub-agents** | ❌ | ✅ ACP + ACPX |
| **Workflow DSL** | ❌ | ✅ Lobster |
| **Media Pipeline** | ❌ | ✅ audio/video/PDF |
| **Python 生態** | ✅ 豐富 | ❌ node_modules |

---

## 6️⃣ 多 Agent 架構

### CoPaw
- **個人助理定位**：專注於單一個人 AI 助理
- **Agent 間通訊**：未明確支援多 Agent 架構
- **擴展方向**：可透過擴展實現多 Agent 通訊

### OpenClaw
- **ACP 協議**：強大的多 Agent 協議
- **sessions_spawn / sessions_yield**：完整子會話管理
- **ACPX Harness**：可委派外部 CLI (Claude Code, Codex, Gemini, Kiro, Cursor)
- **Multi-agent routing**：channel/account/peer/guild/team 路由
- **agentToAgent**：Agent 間通訊工具
- **Cross-agent QMD**：跨 Agent 記憶搜尋
- **Sub-agent registry**：運行中的子代理註冊
- **Delivery Dispatch**：專門的結果回傳系統

### 對比總結

| 項目 | CoPaw | OpenClaw |
|------|-------|----------|
| **多 Agent 支援** | ❌ 不支援 | ✅ 完整 ACP 協議 |
| **子任務委派** | ❌ | ✅ sessions_spawn/yield |
| **外部 CLI 委派** | ❌ | ✅ ACPX |
| **Agent 路由** | ❌ | ✅ 依 channel/peer |
| **Agent 通訊** | ❌ | ✅ agentToAgent |
| **Sub-agent Registry** | ❌ | ✅ |
| **Cross-agent 搜尋** | ❌ | ✅ |

---

## 7️⃣ 安全性與沙盒

### CoPaw
- **個人控制**：所有數據和記憶都在用戶自己的設備上
- **隱私優先**：無雲端依賴，數據不外洩
- **本地運行**：預設在本地運行，網路流量可控
- **Python 安全**：依賴 Python 的安全模型

### OpenClaw
- **Sandbox 後端**：Docker / SSH / 原生 OS 隔離
- **seccomp / AppArmor**：進程級安全策略
- **DM Policy**：pairing / allowlist / open / disabled
- **Tool Policy**：allow/deny/profile/byProvider
- **Tool Result Guard**：驗證工具輸出形狀
- **SecretRef**：敏感資料動態解析
- **Zod Schema 驗證**：嚴格 config 驗證
- **Security Audit**：audit.ts / audit-fs.ts / fix.ts

### 對比總結

| 項目 | CoPaw | OpenClaw |
|------|-------|----------|
| **核心安全模型** | 數據在本地 | Sandbox + Policy + Audit |
| **數據隱私** | ✅ 完全本地控制 | ✅ 本地運行 |
| **Container Isolation** | ❌ | ✅ Docker(seccomp/AppArmor) |
| **DM Policy** | ❌ | ✅ pairing/allowlist/open/disabled |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Security Audit** | ❌ | ✅ audit.ts/fix.ts |
| **Zod Validation** | ❌ | ✅ 拒絕未知 key |
| **SecretRef** | ❌ | ✅ encrypted auth-profile |
| **依賴風險** | Python 生態廣 | Node.js 生態廣 |

---

## 8️⃣ 平台與 Gateway

### CoPaw
- **多聊天 APP 支援**：支援多個主流通訊平台
- **擴展性**：易於擴展新的通訊平台
- **Python 適配器**：各平台的 Python SDK 集成

### OpenClaw
支援 25+ 平台 (含原生 App)：
- WhatsApp, Telegram, Slack, Discord, Signal, Matrix, Mattermost, Feishu, Google Chat, iMessage, IRC, MS Teams, LINE, Nostr, Twitch, WeChat, macOS/iOS/Android App, WebChat...

### 總結

| 項目 | CoPaw | OpenClaw |
|------|-------|----------|
| **平台數量** | 多個 (未精確說明) | 25+ |
| **主要平台** | 主流聊天 APP | 同左 + 更多 |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Web Gateway** | ❌ | ✅ WebChat + Control UI |
| **平台擴展** | ✅ Python 易擴展 | ✅ Plugin 系統 |
| **Voice 支援** | ❌ | ✅ Voice Wake + Talk Mode |

---

## 9️⃣ 架構流程圖

### A. 核心架構對比

**CoPaw: 簡易部署架構**
```text
User Input (通訊平台)
   │
   ▼
[CoPaw Gateway]
   │
   ▼
[Python Agent Core]
   ├── Memory (本地, 完全控制)
   ├── Personalization (用戶自有)
   ├── Tool Execution (Python native)
   └── Respond
```

**OpenClaw: Gateway + Agent Routing**
```text
Input (Channels) → Auth Layer(DM Policy) → Routing Engine(Bindings) → Per-Agent Runtime → LLM → Tool Policy Check → Sandbox → Respond
```

### B. 安裝與部署對比

**CoPaw: 一鍵安裝**
```text
pip install copaw
   │
   ▼
copaw run
   │
   ├── Load config
   ├── Connect to chat platforms
   ├── Initialize memory
   └── Start serving
```

**OpenClaw: npm + Setup**
```text
npm install -g openclaw
   │
   ▼
openclaw setup
   │
   ├── Configure channels
   ├── Setup auth (API keys / OAuth)
   ├── Initialize workspace
   └── Start gateway
```

### C. 安全與隱私對比

| 特性 | CoPaw | OpenClaw |
|------|-------|----------|
| **數據位置** | 🛡️🛡️🛡️🛡️🛡️ 完全在用戶設備 | 🛡️🛡️🛡️🛡️ 本地為主 (可上雲) |
| **安裝難度** | 🟢 `pip install` (簡單) | 🟡 npm + 配置 |
| **記憶控制** | 🟢 完全自主 | 🟢 自主 + 索引 |
| **雲端依賴** | ❌ | ❌ 預設無 |

---

## 📊 總結對比表

| 維度 | CoPaw 優勢 | OpenClaw 優勢 |
|------|------------|---------------|
| **安裝簡易度** | ✅ `pip install copaw` 一鍵安裝 | ⚠️ 需 npm + Node.js |
| **開發者背景** | ✅ AgentScope (阿里雲/清華) | 社群驅動 |
| **個人記憶控制** | ✅ 完全由用戶掌控 | ✅ 本地記憶 + 索引 |
| **多平台支援** | 多個聊天 APP | ✅ 25+ 平台 |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **多 Agent** | ❌ | ✅ ACP + ACPX |
| **工具生態** | Python 第三方庫 | ✅ 12+ 內建 + plugins |
| **向量記憶體** | ❌ | ✅ Hybrid search |
| **安全性** | 數據在本地 | ✅ Sandbox + Policy + Audit |
| **TTS/STT** | ❌ | ✅ OpenAI/11Labs + Whisper |
| **Media** | ❌ | ✅ Image/PDF/Audio/Video |

---

## 🎯 選哪個？

| 需求 | 推薦 |
|------|------|
| **最簡單的安裝與部署** | ✅ CoPaw (`pip install`) |
| **中國開發者/企業使用** | ✅ CoPaw (AgentScope/阿里雲) |
| **個人記憶完全掌控** | ✅ CoPaw |
| **最多通訊平台** | ✅ OpenClaw (25+) |
| **原生 Mobile App** | ✅ OpenClaw |
| **多 Agent 協作** | ✅ OpenClaw |
| **外部 CLI 委派** | ✅ OpenClaw |
| **Python 生態偏好** | ✅ CoPaw |
| **向量長記憶** | ✅ OpenClaw |
| **安全審計** | ✅ OpenClaw |
| **易擴展性** | ✅ CoPaw (Python) / ✅ OpenClaw (Plugins) |
| **Voice Wake** | ✅ OpenClaw |
