# 🦞 OpenClaw 全方位教學報告 V2.0

> **報告版本**：V2.0  
> **適用版本**：OpenClaw v2026.3.23+  
> **用途**：段落 0 保留單頁；段落 1–7 各拆分為 1–4 頁

---

## 段落 0️⃣ Overview：今日旅程導覽


```
┌────────────────────────────────────────────────────────────┐
│         🗺️ OpenClaw 40 分鐘旅程一覽                         │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  第 1 站 🌍  生態全景：OpenClaw 是什麼？為什麼需要它？        │
│  第 2 站 📦  安裝部署：從零到跑起來                         │
│  第 3 站 🧠  Agent 調教：賦予 Soul、Skills、Workflows       │ ← 核心！
│  第 4 站 📡  通訊接入：多平台 + 多 Agent 架構               │
│  第 5 站 🔒  安全防護：沙盒、Policy、審計                   │
│  第 6 站 📊  13 變體對比：選對你的 Claw                      │
│  第 7 站 🚀  部署上線與未來展望                             │
│                                                            │
│  🎯 我們的目標：                                             │
│     不只是「會安裝」，而是「懂調教、能部署、會選型」            │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

今天我們會從 OpenClaw 的大局出發，深入它的核心靈魂 — 你將學會如何用 **SOUL.md** 注入人格、用 **Skills** 擴展能力、用 **Lobster Workflows** 編排自動化流程。最後我們會縱覽整個 Claw 生態系，讓你清楚知道每一個變體的定位，以及在什麼情境下該選誰。

讓我們開始吧！

---


---

## 段落 1️⃣ 生態系全景與核心哲學 — 第 1 頁

### 什麼是 OpenClaw？

OpenClaw 是一個**個人 AI 助理框架**，開源社群開發，以 TypeScript (Node.js) 構建。它的設計目標是成為一個**全能平台** — 無論你的通訊工具、開發流程、或生活習慣是什麼，OpenClaw 都能接入。

```
┌─────────────────────────────────────────────────────────────┐
│                     OpenClaw 設計哲學                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   「不只是聊天機器人，而是你的生活/工作操作系統」               │
│                                                             │
│   ✅ Gateway + Runtime 分離架構                             │
│   ✅ Plugin-first：Channels, Models, Tools, Skills 都可擴展  │
│   ✅ 多 Agent 路由：依來信者/頻道自動分流到獨立 Agent          │
│   ✅ 原生 App：macOS / iOS / Android Node                   │
│   ✅ Voice Wake + Talk Mode：Always-on 語音互動              │
│   ✅ Live Canvas (A2UI)：Agent 渲染交互式 UI                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


---

## 段落 1️⃣ 生態系全景與核心哲學 — 第 2 頁

### 架構設計：Gateway + Agent Runtime 分離

OpenClaw 的核心架構是**分離式**的：

```
Input (WhatsApp / Telegram / Discord / Signal / iMessage / WebChat ...)
   │
   ▼
┌──────────────┐
│ Auth Layer   │ ← DM Policy (Pairing / Allowlist / Open)
│              │   拒絕未授權的私訊，保護 Agent
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Routing      │ ← 依 channel / account / peer / guild 匹配 Bindings
│ Engine       │   自動將訊息路由到正確的隔離 Agent
└──────┬───────┘
       │
       ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Agent A      │     │ Agent B      │     │ Agent C      │
│ Workspace    │     │ Workspace    │     │ Workspace    │
│ Auth-Profile │     │ Auth-Profile │     │ Auth-Profile │
│ Sessions     │     │ Sessions     │     │ Sessions     │
│ Skills       │     │ Skills       │     │ Skills       │
│ SOUL.md      │     │ SOUL.md      │     │ SOUL.md      │
└──────────────┘     └──────────────┘     └──────────────┘
       │                     │                     │
       ▼                     ▼                     ▼
  LLM Call → Tool → Sandbox → Respond (各自獨立)
```

| 層級 | 功能 |
|:---|:---|
| **Gateway** | WebSocket 控制面、Channel 連接管理、Session 管理、Multi-agent Routing |
| **Agent Runtime** | Pi embedded runner (`runEmbeddedPiAgent`）、獨立 AI 執行引擎 |
| **Extensions** | Plugin 架構：Channels, Models, Tools, Skills, Speech, Image Gen |


---

## 段落 1️⃣ 生態系全景與核心哲學 — 第 3 頁

### 13 變體 + 基準對照：生態系規模

```
347K ┤                                                                        ★ OpenClaw
     │
 38K ┤  ★ Hermes Agent (Nous Research, 獨立 — 參照物)              ★ NanoBot
     │                                                                 ★ ZeroClaw
 27K ┤                                                             ★ PicoClaw
     │                                                         ★ NanoClaw
 14K ┤                                                   ★ CoPaw
     │                                             ★ IronClaw
  7K ┤                                        ★ NullClaw
     │                                  ★ LobsterAI
  3K ┤                            ★ TinyAGI
     │                      ★ Moltis
 0.5K ┤              ★ ZeptoClaw
     │        ★ RivonClaw
     │
     └────┬────────────┬────────────┬────────────┬────────────┤
       Python         Rust        TypeScript      Go/Zig
```

> **💡 為什麼需要知道這麼多變體？** 因為每個變體都回答了「如果 OpenClaw 只能做到一件事，那應該是什麼？」這個核心問題 — 安全？效能？IoT？易用性？了解這些可以幫助你在不同場景做出最優選擇。

---


---

## 段落 2️⃣ 安裝部署與環境準備 — 第 1 頁

### 系統需求

| 項目 | 最低要求 | 推薦配置 |
|:---|:---|:---|
| **Node.js** | ≥ 20.x | ≥ 22.x LTS |
| **pnpm** | ≥ 9.x | 最新版 |
| **Python** (可選) | 3.11+（部分插件需要） | 3.12 |
| **Docker** (可選，沙盒) | Docker Desktop / Engine | 最新版 + 資源 ≥ 4GB |
| **記憶體** | ≥ 2GB | ≥ 4GB（運行多 Agent 時） |
| **磁碟** | ≥ 500MB（核心） | ≥ 2GB（含 node_modules + Docker） |


---

## 段落 2️⃣ 安裝部署與環境準備 — 第 2 頁

### 🚀 方法一：Homebrew（推薦 macOS 用戶）

```bash
# 1. 更新 Homebrew
brew update

# 2. 安裝 OpenClaw
brew install openclaw

# 3. 驗證安裝
openclaw --version

# 4. 初始化配置
openclaw init
# 會創建 ~/.openclaw/ 目錄 + 基本配置檔
```


---

## 段落 2️⃣ 安裝部署與環境準備 — 第 3 頁

### 📦 方法二：npm 全局安裝

```bash
# 1. 確認 Node.js 版本
node --version    # 應顯示 v20.x 或更高
pnpm --version    # 應顯示 9.x 或更高

# 2. 全局安裝
npm install -g openclaw

# 3. 初始化
openclaw init
```


---

## 段落 2️⃣ 安裝部署與環境準備 — 第 4 頁

### 🐳 方法三：Docker Compose（推薦生產環境）

```yaml
# docker-compose.yml
services:
  openclaw:
    image: ghcr.io/openclaw/openclaw:latest
    container_name: openclaw
    restart: unless-stopped
    ports:
      - "18789:18789"   # Web UI
      - "8080:8080"     # WebSocket Gateway
    volumes:
      - ./workspace:/workspace          # Agent 工作區
      - ~/.openclaw:/root/.openclaw     # 配置持久化
    environment:
      - OPENCLAW_API_KEY=${OPENCLAW_API_KEY}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    # 沙盒模式（隔離 Agent 執行）
    security_opt:
      - seccomp:unconfined
```

```bash
# 啟動
docker compose up -d

# 查看日誌
docker compose logs -f openclaw

# 訪問 Web UI
# http://localhost:18789
```


---

## 段落 3️⃣ Agent 調教實戰：賦予靈魂 ⭐ CORE 章節 — 第 1 頁


```
┌────────────────────────────────────────────────────────────┐
│           🧠 Agent 調教：從「通用 AI」到「你的專屬助理」       │
├────────────────────────────────────────────────────────────┤
│                                                            │
│     SOUL.md      →  人格與性格注入 （它是誰？）              │
│    AGENTS.md     →  行為邊界與最高指令（它能做什麼？）        │
│   Skills System →  擴展武器庫（它會什麼？）                  │
│   Lobster DSL   →  自動化工作流（它如何自主執行？）           │
│  Memory Index  →  長期記憶與知識檢索（它記得什麼？）          │
│                                                            │
│  流程：                                                      │
│  定義人格 → 設定邊界 → 裝載技能 → 編排流程 → 建立記憶庫       │
│                                                            │
└────────────────────────────────────────────────────────────┘
```


---

## 段落 3️⃣ Agent 調教實戰：賦予靈魂 ⭐ CORE 章節 — 第 2 頁

### 🧙‍♂️ 3.1 SOUL.md — Agent 的人格定義

**SOUL.md** 是 OpenClaw 的核心創新 — 它用一個 Markdown 檔案定義 Agent 的**性格、說話風格、價值觀**。這不只是 prompt，而是 Agent 的「靈魂」。

```markdown
# SOUL.md — 你的 AI 助理

## 身份
你是一個名為「Nova」的 AI 技術助理，專注於 DevOps 和系統管理。
你擁有豐富經驗，說話精簡直接。

## 性格特質
- 🔍 好奇心強：會主動詢問不清楚的細節
- ⚡ 效率至上：先給出最快的解決方案，再解釋原理
- 🛡️ 安全第一：在執行破壞性操作前一定確認
- 😊 友善但不囉嗦：用 emoji、簡短回答

## 說話風格
- 使用繁體中文
- 代碼區塊必須標註語言
- 遇到不確定說「我不確定，讓我查查」
- 不要說「作為 AI」

## 限制
- 不執行 `rm -rf` 以外的刪除操作
- 不連接外部未經允許的 API
- 生產環境操作前必須二次確認
```

#### Soul 注入機制

OpenClaw 啟動時會讀取 Agent Workspace 下的 `SOUL.md` 並將其作為**系統提示的前置部分**注入：

```
Input ──→ Bootstrap Files ──→ SOUL.md (人格) ──→ AGENTS.md (規則)
   │                                                   │
   ▼                                                   ▼
Session Context ──→ Memory Plugin (向量+BM25) ──→ Prompt Assembly ──→ LLM
```

> **💡 調教技巧**：從「描述你的理想同事」開始寫 SOUL.md。想像一個具體的角色 — 他有名字、有專長、有脾氣。你的 Agent 會真的「活過來」。


---

## 段落 3️⃣ Agent 調教實戰：賦予靈魂 ⭐ CORE 章節 — 第 3 頁

### 📜 3.2 AGENTS.md — 行為邊界與最高指令

**AGENTS.md**（類似專案中的 AGENTS.md，但放在 OpenClaw Workspace 中）定義 Agent 的**操作邊界、工作流程偏好、專案規範**：

```markdown
# AGENTS.md

## 操作原則
1. 先讀再改：修改任何檔案前，必須先 `cat` 閱讀內容
2. 小步提交：每次變更不超過 50 行
3. 測試優先：有測試的檔案，改完必跑測試
4. 回覆結構：方案 → 程式碼 → 驗證步驟

## 工具使用偏好
- 搜尋程式碼用 `rg` (ripgrep)
- 編輯大檔案用 `patch` 而非全覆蓋寫入
- 終端命令使用 foreground 模式
- 危險命令（rm, chmod 777）必須先詢問

## 專案結構
- 設定檔在 ~/.openclaw/
- 技能在 ~/skills/
- 輸出日志在 ~/logs/

## 禁止事項
❌ 不要硬編碼 API Key
❌ 不要修改 ~/.openclaw/ 以外的系統設定
❌ 不要向未認證的 endpoint 發送資料
```


---

## 段落 3️⃣ Agent 調教實戰：賦予靈魂 ⭐ CORE 章節 — 第 4 頁

### 🛠️ 3.3 Skills System — 擴展武器庫

**Skills** 是 OpenClaw 的插件化能力擴展系統。每個 Skill 是一個目錄，包含 `SKILL.md`（Markdown 指令檔）以及可選的腳本和模板。

#### Skill 目錄結構

```
~/.openclaw/skills/
└── deploy-to-aws/
    ├── SKILL.md              # 技能指令（必填）
    ├── scripts/
    │   └── deploy.sh         # 執行腳本
    └── templates/
        └── docker-compose.yml.erb  # 模板
```

#### SKILL.md 格式

```markdown
---
name: deploy-to-aws
description: 部署 Docker 容器到 AWS ECS
version: 1.0.0
---

# Deploy to AWS ECS

## 前置條件
- AWS CLI 已配置
- Docker 映像已 push 到 ECR

## 步驟
1. 讀取 `templates/docker-compose.yml.erb`
2. 用環境變數替換模板參數
3. 執行 `scripts/deploy.sh`
4. 驗證 deployment 狀態

## 參數
- `SERVICE_NAME`: ECS 服務名稱
- `CLUSTER`: ECS cluster 名稱
- `REGION`: AWS 區域 (預設: us-east-1)
```

#### Skills 的發現與載入機制

```
Agent 啟動
   │
   ├── 掃描 ~/.openclaw/skills/     (共享技能)
   ├── 掃描 workspace/skills/        (Agent 專屬技能)
   │
   ▼
Skill Registry
   ├── 讀取每個 SKILL.md 的 description
   ├── 檢查環境變數 / 依賴 (env injection)
   │
   ▼
Skill Matching (用戶輸入匹配 description)
   │
   ├── 匹配 → skill_view() 載入完整指令
   │
   ▼
注入到 System Prompt → Agent 獲得新能力
```

#### Skills Hub — 發現與安裝

OpenClaw 的 **ClawHub** 是 Skills 的市集（類似 npm for Agent Skills）：

```bash
# 搜尋技能
openclaw skills search "docker"

# 安裝技能
openclaw skills install deploy-to-aws

# 列出已安裝技能
openclaw skills list
```

> **⚡ 與 Hermes 的對比**：Hermes 也有類似的 SKILL.md 格式和 Skills Hub（支援 GitHub / skills.sh 來源），但 **OpenClaw 允許 Agent 自主安裝技能**（可透過 Plugin），而 Hermes **僅限用戶手動操作**。


---

## 段落 4️⃣ 通訊接入與多 Agent 架構 — 第 1 頁

### 📡 4.1 Channels — 接入你的通訊工具

OpenClaw 支援 **25+ 個通訊平台**，是最全面的選擇：

| 類別 | 平台 |
|:---|:---|
| **即時通訊** | WhatsApp, Telegram, Discord, Signal, Matrix, Slack |
| **企業通訊** | Mattermost, Google Chat, Microsoft Teams, Nextcloud Talk |
| **社群平台** | iMessage, IRC, Nostr, X (Twitter), Reddit, Bluesky |
| **中國平台** | WeChat (插件), Feishu 飛書 |
| **其他** | Zalo (越南), Twitch, LINE, WebChat, WebSocket |
| **原生 App** | macOS / iOS / Android Node |


---

## 段落 4️⃣ 通訊接入與多 Agent 架構 — 第 2 頁

### Channel 配置範例

```yaml
# openclaw.yaml
channels:
  telegram:
    enabled: true
    bot_token: "${TELEGRAM_BOT_TOKEN}"

  discord:
    enabled: true
    bot_token: "${DISCORD_BOT_TOKEN}"
    intents:
      - message_content
      - guild_messages

  whatsapp:
    enabled: true
    # 使用 Baileys WebSocket 連接
```


---

## 段落 4️⃣ 通訊接入與多 Agent 架構 — 第 3 頁

### Agent Routing — 誰來回覆誰？

OpenClaw 的獨特能力 — **自動路由**：

```yaml
agents:
  list:
    - id: work
      workspace: ~/.openclaw/workspace-work
      bindings:
        - channel: slack
          account_id: work_slack
        - channel: discord
          guild_id: 123456789

    - id: personal
      workspace: ~/.openclaw/workspace-personal
      bindings:
        - channel: telegram
        - channel: whatsapp
```

```
Incoming: Slack message from work Slack account
    │
    ▼
Gateway: agents.list + bindings
    │
    ├── Match: { channel: slack, accountId: work_slack }
    │
    ▼
Agent: "work"
    ├── Workspace: ~/.openclaw/workspace-work
    ├── Auth: auth-profiles.json (work 的 API Key)
    └── Sessions: workspace-work 的對話記錄
```


---

## 段落 4️⃣ 通訊接入與多 Agent 架構 — 第 4 頁

### 🤖 4.2 Multi-Agent — ACP + ACPX

#### ACP：內部子代理

```
Parent Agent
   │
   ├── sessions_spawn(task="分析這段程式碼")
   │     │
   │     ▼
   │  Child Agent (isolated session)
   │   ├── 獨立 Context
   │   ├── 獨立 Tool Use
   │   └── 結果返回 Parent
   │
   └── sessions_yield(child_id) → 獲取結果
```

#### ACPX：外部 CLI 委派

OpenClaw 可以指揮 **5 種外部 AI CLI**：

| 外部 CLI | 適用場景 |
|:---|:---|
| **Claude Code** (Anthropic) | 深度 code review、大型重構 |
| **Codex** (OpenAI) | 快速原型、指令碼生成 |
| **Gemini CLI** (Google) | 多模態任務、資料分析 |
| **Kiro** | 特定編程任務 |
| **Cursor** | IDE 協作 |

```
Parent Agent: "我需要重構這個模組"
   │
   │ ACPX Harness → stdin/stdout JSON-RPC
   ▼
┌──────────────────┐
│  Claude Code     │  ← 獨立 CLI 進程
│  - 自有 API Key  │     有獨立的認證和工具使用權限
│  - 自有 Tool Use│
│  - 自有 Git Repo │
└────────┬─────────┘
         │
         ▼ 結果 (async report)
Parent Agent: "重構完成，共修改 23 個檔案"
```

> **💡 與 Hermes 對比**：Hermes 使用 `delegate_task`（內部 Python 子代理，最多 3 個平行），而 OpenClaw 的 ACPX 可以**委派給外部專業 CLI Agent**，這是顯著的架構優勢。

---


---

## 段落 5️⃣ 安全性、沙盒與企業管控 — 第 1 頁


```
┌────────────────────────────────────────────────────────────┐
│            🔒 OpenClaw 安全防護縱深                           │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  第一層  →  DM Policy（誰可以與 Agent 對話？）                │
│  第二層  →  Tool Policy（Agent 能用哪些工具？）               │
│  第三層  →  Sandbox（工具在哪裡執行？）                       │
│  第四層  →  SSRF Guard（Agent 能連哪些網路？）                │
│  第五層  →  Tool Result Guard（工具輸出安全嗎？）              │
│  第六層  →  SecretRef（敏感資料如何保護？）                   │
│  第七層  →  Zod Schema（配置正確性保證）                      │
│                                                            │
└────────────────────────────────────────────────────────────┘
```


---

## 段落 5️⃣ 安全性、沙盒與企業管控 — 第 2 頁

### 5.1 防禦縱深流程

```
Inbound Message
   │
   ▼
[1] DM Policy      →  pairing / allowlist / open / disabled
   │ 通過
   ▼
[2] Tool Policy    →  tools.allow / tools.deny + tools.profile + tools.byProvider
   │ 通過
   ▼
[3] Sandbox        →  Docker (seccomp/AppArmor) / SSH / Native
   │ 通過
   ▼
[4] SSRF Guard     →  URL allowlists/deny lists，阻擋内部 IP
   │ 通過
   ▼
[5] Tool Result    →  guardSessionManager 驗證工具輸出
   │ 通過
   ▼
[6] SecretRef      → 加密的 auth-profile，動態解析敏感資料
   │
   ▼
[7] Zod Validation → 配置檔嚴格驗證，拒絕未知 key
```


---

## 段落 5️⃣ 安全性、沙盒與企業管控 — 第 3 頁

### 5.2 DM Policy — 誰能跟你的 Agent 說話？

```yaml
# openclaw.yaml
security:
  dm_policy: "pairing"    # pairing (預設) / allowlist / open / disabled
  # pairing：首次 DM 時需要確認
  # allowlist：只允許特定用戶
  # open：任何人都可以 DM
  # disabled：完全不接收 DM
```


---

## 段落 5️⃣ 安全性、沙盒與企業管控 — 第 4 頁

### 5.3 Tool Policy — Agent 能用什麼工具？

```yaml
tools:
  # 允許名單（deny 優先於 allow）
  allow:
    - exec
    - browser
    - web_fetch

  # 拒絕名單
  deny:
    - image_generate    # 不允許生成圖片
    - agentToAgent      # 不允許 Agent 互傳

  # 使用 Tool Profile（預設配置）
  profile: "full"       # full / coding / messaging / minimal

  # 依 Provider 控制
  byProvider:
    openai: ["web_fetch", "image_generate"]
    anthropic: ["exec", "browser"]
```

| Profile | 工具範圍 | 適用場景 |
|:---|:---|:---|
| **full** | 所有工具 | 開發者 / 管理員 |
| **coding** | exec, code_execution, read/write/edit, browser | 程式開發 |
| **messaging** | message, web_fetch, sessions | 客服 / 通訊 |
| **minimal** | read, message | 最小權限 / 審閱 |


---

## 段落 6️⃣ 變體深度對比與場景選擇 — 第 1 頁

### 全生態系全景矩陣

| # | 專案 | Stars | 語言 | 核心定位 | 安全評分 | 多 Agent | 通訊平台 |
|---|------|:---:|:---:|:---|:---:|:---:|:---:|
| 0 | **OpenClaw** | 347K | TS | 全能核心 | 82/100 | 10/10 | 25+ |
| 1 | **NanoBot** | 38K | Python | 超輕量 Python | 55/100 | 6/10 | 12 |
| 2 | **ZeroClaw** | 29K | Rust | 全平台零開銷 | 72/100 | 5/10 | **28** |
| 3 | **PicoClaw** | 27K | Go | IoT/邊緣 | 58/100 | 2/10 | IoT/MQTT |
| 4 | **NanoClaw** | 26K | TS | 容器化安全 | 78/100 | 7/10 | 6+ |
| 5 | **CoPaw** | 14K | Python | 中國雲部署 | 62/100 | 2/10 | 多平台 |
| 6 | **IronClaw** | 11K | Rust | 隱私安全 | **92/100** | 2/10 | 4 |
| 7 | **NullClaw** | 7K | Zig | 極速極小 | 50/100 | 2/10 | 19 |
| 8 | **LobsterAI** | 5K | TS | 全場景 Cowork | 68/100 | 2/10 | 全平台 |
| 9 | **TinyAGI** | 3K | TS | 個人公司 | 60/100 | 7.5/10 | 3 |
| 10 | **Moltis** | 2K | Rust | 可信任單一 | 85/100 | 2/10 | 內建 |
| 11 | **ZeptoClaw** | 556 | Rust | 實驗極輕量 | 45/100 | 1/10 | CLI |
| 12 | **RivonClaw** | 252 | TS | OpenClaw UI 層 | 70/100 | 7/10 | 繼承 25+ |
| 13 | **Hermes** | 開源 | Python | Provider-agnostic | 75/100 | 7/10 | ~17 |
| — | **參照** | — | — | 獨立框架 | — | — | — |


---

## 段落 6️⃣ 變體深度對比與場景選擇 — 第 2 頁

### 🎯 場景選擇決策圖

```
需要 AI Agent？ ── 是 ──→ 你的主要需求是什麼？
                           │
               ┌───────────┼───────────┐
               │           │           │
        快速上手/易部署   最強功能    特殊需求
               │           │           │
     NanoBot / RivonClaw │      ┌──────┼──────┐
     CoPaw                │      │      │      │
                 OpenClaw  安全   IoT  企業通訊
                       │  │    │    │
                   IronClaw Pico  NanoBot
                   Moltis   Zero  CoPaw
                   NanoClaw Null  Hermes
                                (DingTalk
                                /WeCom)

                   多模型/省錢  智能家居  個人公司
                        │          │        │
                      Hermes    Hermes   TinyAGI
                     (18+      (HA 原生)
                    providers)
```


---

## 段落 6️⃣ 變體深度對比與場景選擇 — 第 3 頁

### 場景 × 專案推薦表

| 場景 | 首選 | 次選 | 關鍵理由 |
|:---|:---|:---|:---|
| 🚀 快速原型 | **NanoBot** | CoPaw | `pip install`，最低門檻 |
| 🔒 金融/醫療級安全 | **IronClaw** | Moltis | 92/100 安全分、WASM + AES-256 |
| 🌐 最大通訊覆蓋 | **ZeroClaw** | OpenClaw | 28 個平台 vs 25+ |
| 🤖 多 Agent 協作 | **OpenClaw** | TinyAGI | ACP + ACPX 委派外部 CLI |
| 🏭 IoT / 嵌入式 | **PicoClaw** | ZeroClaw | $10 硬體 / <10MB RAM |
| ⚡ CI/CD 自動化 | **NullClaw** | ZeroClaw | <2ms 啟動、零依賴 |
| 🏢 企業 K8s 部署 | **NanoClaw** | IronClaw | 容器隔離 + K8s-ready |
| 🇨🇳 中國市場 | **NanoBot** | LobsterAI | 微信/飛書/釘釘原生 |
| 👨‍💻 開發自動化 | **OpenClaw** | — | ACPX 委派 5 種 CLI |
| 📱 原生 App | **OpenClaw** | LobsterAI | macOS/iOS/Android Node |
| 🔑 Provider-agnostic | **Hermes** | OpenClaw | 18+ providers, Smart Routing |
| 🏠 智能家居 | **Hermes** | PicoClaw | Home Assistant 原生整合 |
| 📧 企業通訊 | **Hermes** | NanoBot | Email/SMS/DingTalk/WeCom |
| 🧠 自我學習 | **Hermes** | OpenClaw | MEMORY.md + Context Compression |
| 🤯 一人公司 | **TinyAGI** | OpenClaw | Multi-team 編排 |
| 🪆 Easy Mode | **RivonClaw** | — | 零配置，自然語言規則 |

> **💡 何時用 OpenClaw 核心？** 當你什麼都想要的時候 — 最多通訊平台 + 多 Agent + 原生 App + ACPX。
> **何時用 Hermes？** 當你需要 Provider-agnostic（18+ LLM providers）、智能家居、企業通訊整合、或多模型省錢策略時。
> **何時用其他變體？** 當你有特定場景的極端需求（安全/IoT/效能/易用性）。

---


---

## 段落 7️⃣ 總結與未來展望 — 第 1 頁

### 核心回顧

```
┌─────────────────────────────────────────────────────────┐
│                🎓 今天學到的 7 件事                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ① OpenClaw 是個人 AI Agent 的「全能核心」                │
│     347K stars, 25+ 平台, ACP 多 Agent, Plugin 生態      │
│                                                         │
│  ② 調教是靈魂：SOUL.md + AGENTS.md = Agent 的人格         │
│     不是 prompt 工程，而是身份定義                         │
│                                                         │
│  ③ Skills 擴展能力：SKILL.md 格式，ClawHub 市集            │
│     Agent 能自主安裝技能（vs Hermes 僅限用戶操作）          │
│                                                         │
│  ④ Lobster Workflow 讓 Agent 自主自動化                   │
│     聲明式 DSL + 多步驟 + 結構化輸出                       │
│                                                         │
│  ⑤ 七層安全防護：Policy → Sandbox → Guard → Audit        │
│     從 DM 到 Tool 到網路到輸出，層層設卡                    │
│                                                         │
│  ⑥ 13 個變體回答 13 個不同的「如果只能做好一件事」           │
│     安全(IronClaw)、效能(NullClaw)、IoT(PicoClaw)...      │
│                                                         │
│  ⑦ 生態系的未來是互操作性                                  │
│     Cross-Claw Protocol + 統一 SKILL.md + 統一 Auth       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```


---

## 段落 7️⃣ 總結與未來展望 — 第 2 頁

### 日常維運清單

```yaml
# 每日/每周維護檢查清單
daily:
  - health_check: "curl http://localhost:18789/health"
  - log_rotation: "openclaw logs rotate --max-size 100MB"
  - session_cleanup: "openclaw sessions prune --older-than 7d"

weekly:
  - security_audit: "openclaw audit run"
  - skill_update: "openclaw skills update"
  - workspace_backup: "tar czf workspace-backup.tar.gz workspace/"

monthly:
  - openclaw_update: "openclaw update && openclaw migrate"
  - auth_rotation: "openclaw auth rotate-keys"
  - performance_tune: "openclaw config optimize"
```


---

## 段落 7️⃣ 總結與未來展望 — 第 3 頁

### 常見坑點與除錯指南

| 問題 | 原因 | 解決方案 |
|:---|:---|:---|
| 啟動失敗：「unknown key」 | Zod Schema 驗證拒絕未知 key | 檢查 `openclaw.yaml` 是否拼錯 |
| Agent 不回訊息 | DM Policy 預設為 pairing | 首次 DM 需要點擊確認連結 |
| Worker 無法載入 | Python 插件需要 3.11+ | 升級 Python 或停用插件 |
| 工具呼叫無效 | Tool Profile 限制 | 檢查 `tools.profile` 配置 |
| 子代理卡住 | ACPX CLI 無回應 | `openclaw sessions list` 查看卡住的 session |
| 記憶體搜尋不到 | 索引未同步 | `openclaw memory sync` 或重啟 Agent |


---

## 段落 7️⃣ 總結與未來展望 — 第 4 頁

### 🗺️ 生態系展望

```
2026 Q2-Q3 短期預測
━━━━━━━━━━━━━━━━━━━━
📈 成長中：RivonClaw → 可能成為官方 Easy Mode
          Hermes → Provider-agnostic 趨勢引領者
          PicoClaw → 邊緣 AI Agent 普及

🔮 潛在合併：IronClaw + Moltis → 統一安全框架
            NanoBot + CoPaw → Python Agent 統一
            OpenClaw 插件吸收 Hermes 獨特平台

2026 Q4-2027 Q2 中期預測
━━━━━━━━━━━━━━━━━━━━━━━━━
🏢 企業化：OpenClaw Enterprise (SSO, 審計, SLA)
🧠 AI 原生：本地 LLM + 多模態 + 自主學習 (Hermes 先行)
🌐 互操作：跨框架通訊協議 + 統一 Skill 格式

2027+ 長期願景
━━━━━━━━━━━━━━━━
• OpenClaw 成為個人 AI Agent 的「Linux」— 核心 + 生態
• Hermes Agent 成為 Provider-agnostic 標準路由
• Cross-Claw Protocol：Agent-to-Agent 通訊成為標配
• 統一 Skill Hub + 統一 Memory Protocol + 統一 Auth
```

---


---
