# IronClaw vs OpenClaw —— 深度比較報告

> 整理日期：2026-04-04 | 版本參考：IronClaw (nearai/ironclaw), OpenClaw v2026.3.23+ | Repo: nearai/ironclaw ⭐ 11,361

---

## 1️⃣ 核心定位

| 項目 | IronClaw | OpenClaw |
|------|----------|----------|
| **開發者** | NEAR AI (區塊鏈/AI 團隊) | 開源社群 |
| **設計理念** | OpenClaw inspired，專注於**隱私與安全** | 個人 AI 助理，強調 plugin 生態 + 外部 agent 委派 |
| **核心差異** | **安全與隱私優先** — WASM 沙箱、AES-256-GCM、Prompt Injection 防禦 | **生態與平台** — 最多通訊平台覆蓋 + ACP 委派架構 |
| **語言** | Rust | TypeScript (Node.js) |
| **Stars** | 11,361 ⭐ | ~57,000+ ⭐ |
| **License** | Apache-2.0/MIT | GPL-3.0 |
| **技術棧** | Rust, PostgreSQL 15+, NEAR AI OAuth, WASM + Docker | TypeScript, Node.js, 多平台 SDK |
| **DeepWiki** | 14 章 | 完整 |
| **主要場景** | 高安全性需求、企業級部署、隱私敏感應用 | 個人助理、最大通訊覆蓋、多 Agent 協作 |

---

## 2️⃣ 架構比較

### IronClaw

- **WASM 沙箱**：核心隔離機制，WebAssembly 提供記憶體/類型安全
- **PostgreSQL + pgvector**：企業級 DB + 向量嵌入
- **Dynamic Tool Building**：動態構建工具
- **Gateway 層**：4 種 Gateway（REPL, HTTP Webhooks, WASM 通道, Web Gateway）
- **NEAR AI OAuth**：去中心化身份認證

### OpenClaw

- **Gateway + Agent Runtime**：WebSocket 控制面 + Pi embedded runner
- **Plugin architecture**：channels, models, tools, skills, speech
- **Multi-agent routing**：依 channel/account/peer 路由
- **Sandbox 可選**：Docker/seccomp/AppArmor

| 項目 | IronClaw | OpenClaw |
|------|----------|----------|
| **核心架構** | WASM 沙箱 + PostgreSQL | Gateway + Agent Runtime + Plugins |
| **程式語言** | ✅ Rust (零成本/記憶體安全) | TypeScript |
| **隔離模型** | WASM 沙箱 | Docker / SSH 容器 |
| **資料庫** | PostgreSQL + pgvector | Markdown 檔案索引 |
| **通訊平台** | 4 種 | 25+ 種 |
| **Plugin 系統** | ❌ | ✅ 完整插件 |
| **原生 App** | ❌ | ✅ macOS/iOS/Android |
| **Voice Wake** | ❌ | ✅ |

---

## 3️⃣ LLM Provider 支援

### IronClaw
- 透過 NEAR AI 生態整合 + 直接 API 連線
- **NEAR AI OAuth**：區塊鏈去中心化認證

### OpenClaw
- OpenAI, Anthropic, Google, Z.ai 等多 Provider
- Auth Profiles, Fallback chains, Model allowlist, Custom providers

| 項目 | IronClaw | OpenClaw |
|------|----------|----------|
| **Provider 數量** | ⚠️ NEAR AI + 直連 | ✅ 多家原生 |
| **Fallback chains** | ❌ | ✅ |
| **OAuth** | ✅ NEAR AI OAuth | ✅ OpenAI/Anthropic |
| **Model allowlist** | ❌ | ✅ |
| **Credential 加密** | ✅ AES-256-GCM | ✅ SecretRef |

---

## 4️⃣ 記憶與學習

### IronClaw
- **PostgreSQL + pgvector**：關聯式持久化 + 向量搜尋
- **企業級 ACID**：streaming replica, pg_dump 備份
- **PostgreSQL FTS**：全文搜尋

### OpenClaw
- **MemoryIndexManager**：Markdown 檔案索引
- **Hybrid search**：向量 + FTS
- **Embedding provider**：可配置外部 embedding
- **Skills System**：SKILL.md + ClawHub

| 項目 | IronClaw | OpenClaw |
|------|----------|----------|
| **Memory Store** | ✅ PostgreSQL (ACID) | Markdown 檔案 |
| **向量搜尋** | ✅ pgvector | ✅ Embedding provider |
| **FTS 搜尋** | ✅ PostgreSQL FTS | ✅ FTS |
| **備份/恢復** | ✅ pg_dump | ❌ |
| **技能系統** | ❌ | ✅ Skills Hub |

---

## 5️⃣ 工具與擴展

### IronClaw
- **動態工具構建**：根據任務需求動態生成
- **WASM 沙箱工具執行**
- **NEAR AI 工具整合**

### OpenClaw
- 12+ 內建工具 + Plugin tools (Lobster, LLM Task, Diffs, OpenProse)
- Tool profiles: full/coding/messaging/minimal
- 9 個 Tool Group
- CronService: heartbeat, webhook, session scopes
- Media: image_generate, Whisper, PDF, TTS

| 項目 | IronClaw | OpenClaw |
|------|----------|----------|
| **工具執行** | ✅ WASM 沙箱 | Docker/seccomp/AppArmor |
| **動態工具** | ✅ | ❌ |
| **內建工具數** | ⚠️ 較少 | ✅ 12+ |
| **排程任務** | ❌ | ✅ CronService |
| **圖片/TTS** | ❌ | ✅ |
| **Media Pipeline** | ❌ | ✅ audio/video/PDF |

---

## 6️⃣ 多 Agent 架構

### IronClaw
- **WASM 隔離**：Agent 在 WASM 中安全運行
- **Web Gateway (SSE/WS)**：多 Agent 通訊
- **PostgreSQL 共享狀態**

### OpenClaw
- **ACP 協議**：sessions_spawn / sessions_yield
- **ACPX Harness**：外部 CLI 委派 (Claude Code, Codex, Gemini, Kiro, Cursor)
- **Multi-agent routing**：channel/account/peer/guild/team
- **agentToAgent / Cross-agent QMD / Sub-agent registry / Delivery Dispatch**

| 項目 | IronClaw | OpenClaw |
|------|----------|----------|
| **Agent 隔離** | ✅ WASM | ✅ Docker/SSH |
| **子任務委派** | ❌ | ✅ ACP |
| **外部 CLI** | ❌ | ✅ ACPX |
| **Agent 通訊** | ✅ Web Gateway | ✅ agentToAgent |
| **Sub-agent Registry** | ❌ | ✅ |

---

## 7️⃣ 安全性與沙盒（IronClaw 最強項目）

### IronClaw
- **WASM 沙箱**：記憶體/類型安全，無系統呼叫風險
- **AES-256-GCM**：工業級加密
- **Prompt Injection 防禦**：專項設計
- **NEAR AI OAuth**：去中心化認證

### OpenClaw
- Docker/seccomp/AppArmor
- DM Policy, Tool Policy (allow/deny/profile), Tool Result Guard
- SecretRef, Zod Schema Validation, Security Audit, Capability Model

| 項目 | IronClaw | OpenClaw |
|------|----------|----------|
| **沙箱** | ✅ WASM (零系統呼叫) | ✅ Docker (可配置) |
| **加密** | ✅ AES-256-GCM | ✅ SecretRef + encrypted auth |
| **Prompt 防禦** | ✅ 專項設計 | ⚠️ Tool Policy/SSRF |
| **認證** | ✅ NEAR AI OAuth (去中心化) | ✅ 標準 OAuth |
| **Tool Policy** | ❌ | ✅ allow/deny/profile |
| **Security Audit** | ❌ | ✅ 完整 |
| **Zod Validation** | ❌ | ✅ |

---

## 8️⃣ 平台與 Gateway

### IronClaw：4 種 Gateway
| Gateway | 說明 |
|---------|------|
| REPL | 本機交互式 CLI |
| HTTP Webhooks | 標準 HTTP 回調 |
| WASM 通道 | Telegram, Slack |
| Web Gateway | SSE/WS |

### OpenClaw：25+ 平台
WhatsApp, Telegram, Slack, Discord, Signal, Matrix, Feishu, Google Chat, iMessage, MS Teams, LINE, WeChat, macOS/iOS/Android App ...

| 項目 | IronClaw | OpenClaw |
|------|----------|----------|
| **平台數** | 4 | 25+ |
| **Telegram** | ✅ | ✅ |
| **Slack** | ✅ | ✅ |
| **Discord** | ❌ | ✅ |
| **WhatsApp** | ❌ | ✅ |
| **原生 App** | ❌ | ✅ |
| **REPL** | ✅ | ❌ |
| **Voice** | ❌ | ✅ |

---

## 9️⃣ 架構流程圖

### A. 核心架構
**IronClaw**
```
Input (REPL/Webhook/Telegram/Slack/Web)
   ▼
[IronClaw Gateway]
   ▼
[WASM Sandboxed Agent]
   ├── Prompt Injection Defense
   ├── Credential Protection (AES-256-GCM)
   └── Tool Execution (WASM)
   ▼
[PostgreSQL + pgvector]
```

**OpenClaw**
```
Input → Auth Layer → Routing Engine → Per-Agent Runtime → LLM → Tool Policy → Sandbox → Respond
```

### B. 安全流程
**IronClaw**
```
Input → NEAR AI OAuth → Prompt Injection Defense → WASM Sandbox → AES-256-GCM Credential Access → PostgreSQL
```

**OpenClaw**
```
DM Policy → Tool Policy → Sandbox(Docker/seccomp/AppArmor) → SSRF → Tool Result Guard → Audit Log
```

### C. 安全评分
| 特性 | IronClaw | OpenClaw |
|------|----------|----------|
| 沙箱強度 | 🛡️🛡️🛡️🛡️🛡️ | 🛡️🛡️🛡️🛡️ |
| 加密 | 🛡️🛡️🛡️🛡️🛡️ AES-256-GCM | 🛡️🛡️🛡️🛡️ SecretRef |
| Prompt 防禦 | 🛡️🛡️🛡️🛡️🛡️ | 🛡️🛡️🛡️ |
| 審計 | ❌ | ✅ 完整 |

---

## 📊 總結對比表

| 維度 | IronClaw | OpenClaw |
|------|----------|----------|
| **WASM 沙箱** | ✅ | ❌ |
| **AES-256-GCM** | ✅ | ❌ |
| **Prompt Injection** | ✅ 專項防禦 | ⚠️ 一般防護 |
| **PostgreSQL** | ✅ | ❌ |
| **NEAR AI OAuth** | ✅ | ❌ |
| **通訊平台** | ⚠️ 4 種 | ✅ 25+ |
| **原生 App** | ❌ | ✅ |
| **多 Agent** | ⚠️ 基本 | ✅ ACP + ACPX |
| **Plugin** | ❌ | ✅ |
| **安全審計** | ❌ | ✅ |
| **Tool Policy** | ❌ | ✅ |
| **Rust vs TS** | ✅ Rust | TypeScript |

---

## 🎯 選哪個？

| 需求 | 推薦 |
|------|------|
| **最高安全級別** | ✅ IronClaw |
| **AES-256-GCM 加密** | ✅ IronClaw |
| **Prompt Injection 防護** | ✅ IronClaw |
| **WASM 沙箱** | ✅ IronClaw |
| **PostgreSQL 存儲** | ✅ IronClaw |
| **区块链整合** | ✅ IronClaw |
| **最多通訊平台** | ✅ OpenClaw |
| **原生 Mobile** | ✅ OpenClaw |
| **多 Agent 協作** | ✅ OpenClaw |
| **安全審計** | ✅ OpenClaw |
| **Plugin 生態** | ✅ OpenClaw |
| **Rust 偏好** | ✅ IronClaw |
