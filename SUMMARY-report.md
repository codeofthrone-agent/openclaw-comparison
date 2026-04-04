# 🦞 OpenClaw 生態系全面總結報告

> **整理日期**：2026-04-04  
> **版本**：OpenClaw v2026.3.23+ 生態系 v1.0  
> **資料來源**：13 份深度比較報告 + 生態系全貌報告  
> **用途**：40 分鐘教學報告基礎文件

---

## 📑 目錄

- [1. 📊 生態系全景](#1--生態系全景)
- [2. 🏗️ 架構模式分類](#2--架構模式分類)
- [3. 🔒 安全性深度評測](#3--安全性深度評測)
- [4. 📡 通訊平台覆蓋](#4--通訊平台覆蓋)
- [5. 🧠 記憶與學習](#5--記憶與學習)
- [6. 🤖 多 Agent 能力](#6--多-agent-能力)
- [7. 💾 效能與資源](#7--效能與資源)
- [8. 🎯 使用場景推薦矩陣](#8--使用場景推薦矩陣)
- [9. 📝 結論與建議](#9--結論與建議)
- [10. 🗺️ 生態系演進預測](#10--生態系演進預測)

---

## 1. 📊 生態系全景

### 13 變體 + OpenClaw 核心 + Hermes · 全景對比矩陣

```
┌─────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                    OPENCLAW 生態系全景矩陣 (13 + 1 + 1)                                         │
├─────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                                 │
│  400K ┤                                                                                        ★ OpenClaw      │
│       │                                                                                  ★ NanoBot             │
│  300K ┤                                                                              ★ ZeroClaw                │
│ Stars │                                                                          ★ PicoClaw                    │
│       │                                                                        ★ NanoClaw                      │
│  200K ┤                                                                  ★ CoPaw                               │
│       │                                                            ★ IronClaw                                   │
│  100K ┤                                                    ★ NullClaw                                            │
│       │                                               ★ LobsterAI                                               │
│       │                                          ★ TinyAGI                                                      │
│   50K ┤                                    ★ Moltis                                                              │
│       │                            ★ ZeptoClaw                                                                   │
│       │                     ★ RivonClaw                                                                          │
│       │  ★ Hermes (Nous Research, 獨立)                                                                           │
│       └────┬────────────┬────────────┬────────────┬────────────┬────────────┬────────────┬────────────┤          │
│          Python        Rust         TypeScript      Go           Zig         Rust         TS/Rust      分類       │
│                                                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

### 15 項目完整對比矩陣

| # | 專案 | Stars | 語言 | 定位 | 二進制/部署 | 通訊平台 |
|---|------|-------|------|------|-------------|----------|
| 0 | **OpenClaw** (核心) | ~347K | TypeScript | 全能個人 AI 助理 | npm + Node.js | **25+** |
| 1 | **NanoBot** | 37,801 | Python | 超輕量 Python | `pip install` | **12** |
| 2 | **ZeroClaw** | 29,342 | Rust | 全平台零開銷 | 單一靜態 ~8.8MB | **28** |
| 3 | **PicoClaw** | 27,275 | Go | IoT/邊緣設備 | Go 編譯 + Docker | IoT/MQTT |
| 4 | **NanoClaw** | 26,373 | TypeScript | 容器化安全隔離 | Docker Compose/K8s | **6+** |
| 5 | **CoPaw** | 14,363 | Python | 中國雲端部署 | `pip install` | 多平台 |
| 6 | **IronClaw** | 11,361 | Rust | 安全隱私優先 | Rust + PostgreSQL | 4 |
| 7 | **NullClaw** | 7,024 | Zig | 極速極小 | **~678 KB** 靜態 | **19** |
| 8 | **LobsterAI** | 4,826 | TypeScript | 全場景 Cowork | 跨平台原生 App | 多平台 |
| 9 | **TinyAGI** | 3,462 | TypeScript | 個人公司 Agent | Node.js | 3 |
| 10 | **Moltis** | 2,479 | Rust | 可信任單一伺服 | 單一 Binary | 內建多通道 |
| 11 | **ZeptoClaw** | 556 | Rust | 實驗極輕量 | Rust 編譯 | CLI |
| 12 | **RivonClaw** | 252 | TypeScript | OpenClaw UI 層 | Wrapper | 繼承 25+ |
| 13 | **Hermes Agent** | 開源 | Python | 通用型 AI Agent | pip/docker | **~17** |

### 生態系總計數值

```
╔═══════════════════════════════════╗
║   OpenClaw 生態系統計總覽         ║
╠═══════════════════════════════════╣
║  專案總數    │  15 (13+OpenClaw+Hermes)║
║  總 Stars    │  ~510,000+         ║
║  總 Forks    │  ~99,000+          ║
║  語言種類    │  6 (TS/P/R/Go/Z/🐛)║
║  開源協議    │  MIT/Apache/GPL    ║
║  DeepWiki    │  ✅ 全部 15 個       ║
╚═══════════════════════════════════╝
```

---

## 2. 🏗️ 架構模式分類

### 六大架構模式分組

```
                    ┌─────────────────────────────────────────────┐
                    │           OpenClaw 生態系架構分類            │
                    └─────────────────────────┬───────────────────┘
                                              │
            ┌──────────┬──────────┬──────────┬┴──────────┬──────────┐
            ▼          ▼          ▼          ▼           ▼          ▼
       ┌────────┐ ┌────────┐┌──────────┐┌────────┐┌──────────┐┌────────┐
       │全能核心│ │輕量/通用│ │極致效能  ││安全/隔離││企業/全場景│ │UI擴展 │
       └───┬────┘ └───┬────┘└────┬─────┘└───┬────┘└────┬─────┘└───┬────┘
           │          │          │          │          │          │
      OpenClaw │  NanoBot  │  ZeroClaw  │ IronClaw │ LobsterAI │ RivonClaw
               │  Hermes   │  NullClaw  │ Moltis   │ TinyAGI   │
               │  NanoClaw │  PicoClaw  │ NanoClaw │           │
               │  CoPaw    │  ZeptoClaw │          │           │
```

> **註：Hermes Agent** 為 Nous Research 開發的獨立通用型 AI Agent，設計理念為「多平台 gateway + 自我學習 + Provider-agnostic」，歸入輕量/通用型但具有獨特的 Provider-agnostic 架構。

### 詳細分類表

#### 🟦 A. 全能核心型（1 個）

| 專案 | 特徵 |
|------|------|
| **OpenClaw** | Gateway + Agent Runtime 分離、25+ 平台、ACP 多 Agent、Plugin 生態、原生 App、Live Canvas、Voice Wake |

> **設計哲學**：「功能優先、最大覆蓋」— 以 TypeScript 構建最完整的個人 AI Agent 框架。

#### 🟩 B. 輕量備选/通用型（3+1 個）

| 專案 | 特點 | 差異化 |
|------|------|--------|
| **NanoBot** | Python 單體、`pip install`、代碼量少 99% | MCP 擴展中國平台整合 |
| **NanoClaw** | 容器級隔離、Anthropic SDK 為核心 | 每 Agent = 容器天然沙盒 |
| **CoPaw** | Python、阿里/清華 AgentScope 團隊 | 個人記憶完全自主隱私優先 |
| **Hermes Agent** | 通用型 Agent、多平台 gateway、Provider-agnostic | 18 toolsets + 6 種執行後端 + Profiles 多實例 |

> **設計哲學**：「降低入門門檻 / 通用靈活性」— 用更簡單的語言或架構提供核心功能，或以 Provider-agnostic 設計實現跨模型無縫切換。

#### 🟧 C. 極致效能型（4 個）

| 專案 | 語言 | 核心特色 |
|------|------|----------|
| **ZeroClaw** | Rust 2024 | **28 平台最多**、<5MB RAM、<10ms 冷啟動、硬體集成(ESP32/Pi)、E-stop |
| **NullClaw** | Zig | **~678 KB 最小**、<2ms 啟動、~1MB RSS、零依賴 |
| **ZeptoClaw** | Rust | Ultra-lightweight、longterm_memory、吉祥物 Zippy（實驗中） |
| **PicoClaw** | Go | $10 硬體、Sipeed 開發、MQTT/Serial/GPIO（IoT 原生） |

> **設計哲學**：「效能優先、極簡部署」— 犧牲功能換取速度、體積和资源效率。

#### 🟨 D. 安全隔離型（3 個）

| 專案 | 安全機制 | 特色 |
|------|----------|------|
| **IronClaw** | WASM 沙箱+ AES-256-GCM、Prompt Injection 防禦 | NEAR AI OAuth 去中心化認證 |
| **Moltis** | WASM sandbox + Rust 單一 Binary | 可審計、MCP 工具標準 |
| **NanoClaw** | Docker 容器隔離（預設） | K8s-ready、高 fork 率(39%) |

> **設計哲學**：「安全即核心」— 將隔離和審計視為第一優先級。

#### 🟪 E. 企業/全場景型（2 個）

| 專案 | 企業特色 | 獨特能力 |
|------|----------|----------|
| **LobsterAI** | 網易有道開發、Cowork Mode | macOS + Windows + Linux + Mobile 全平台 |
| **TinyAGI** | Agent Teams 編排器 | Multi-agent/Multi-team/Multi-channel、一人公司 |

> **設計哲學**：「組織級協作」— 從個人助理升級為團隊/組織級 AI 平台。

#### 🟫 F. UI 擴展層型（1 個）

| 專案 | 定位 | 關係 |
|------|------|------|
| **RivonClaw** | OpenClaw Easy Mode | 依賴 OpenClaw、桌面 App + 自然語言規則 + Agent 自行進化 |

> **設計哲學**：「開箱即用」— 在強大框架之上構建零配置入口。

---

## 3. 🔒 安全性深度評測

### 綜合安全评分 + 排名

評分標準：沙盒強度(25%)、加密(20%)、策略引擎(20%)、審計能力(15%)、語言安全(10%)、攻擊面(10%)

```
安全评分排名 (滿分 100)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

IronClaw    ████████████████████████████████████████  92/100  ★★★★★
   WASM + AES-256-GCM + Prompt Injection 防禦 + Rust

Moltis      ████████████████████████████████░░░░░░░░  85/100  ★★★★★
   WASM 沙盒 + Rust 型別安全 + 可審計 + 單一 Binary

NanoClaw    ██████████████████████████████▒░░░░░░░░░  78/100  ★★★★☆
   容器級隔離（預設）+ K8s policies + Docker 安全

OpenClaw    █████████████████████████████▒░░░░░░░░░░  82/100  ★★★★★
   Docker/seccomp/AppArmor + Tool Policy + Audit + Zod

Hermes      ████████████████████████████▒░░░░░░░░░░░  75/100  ★★★★☆
   Defense-in-Depth + Tirith + SSRF + 6 種沙盒後端

LobsterAI   ██████████████████████████▒░░░░░░░░░░░░░  68/100  ★★★☆☆
   企業安全標準 + 跨平台安全（推斷）

CoPaw       █████████████████████░░░░░░░░░░░░░░░░░░░  62/100  ★★★☆☆
   數據完全本地 + Python 生態 + 隱私優先

ZeroClaw    ████████████████████▒░░░░░░░░░░░░░░░░░░░  72/100  ★★★★☆
   Rust 記憶體安全 + Landlock + Bubblewrap + E-stop

TinyAGI     ██████████████████▒░░░░░░░░░░░░░░░░░░░░░  60/100  ★★★☆☆
   隔離工作區 + 成熟度高(56 issues) + TS 生態

RivonClaw   ██████████████████▒░░░░░░░░░░░░░░░░░░░░░  70/100  ★★★☆☆
   繼承 OpenClaw 安全 + 額外 wrapper 層風險

NanoBot     ██████████████▒░░░░░░░░░░░░░░░░░░░░░░░░░  55/100  ★★☆☆☆
   Docker 局部 + 最小攻擊面（代碼少）+ SDK 依賴

PicoClaw    ██████████████▒░░░░░░░░░░░░░░░░░░░░░░░░░  58/100  ★★☆☆☆
   Docker 邊緣 + Go 型別安全 + 硬體安全（TrustZone）

NullClaw    ████████████▒░░░░░░░░░░░░░░░░░░░░░░░░░░░  50/100  ★★☆☆☆
   極小攻擊面(678KB) + Zig 記憶體安全、無內建沙盒

ZeptoClaw   ██████████▒░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  45/100  ★★☆☆☆
   Rust 編譯期安全 + 最小攻擊面、實驗階段不建議上線
```

### 安全能力詳細對比

| 安全能力 | OpenClaw | Hermes | IronClaw | NanoClaw | Moltis | ZeroClaw |
|----------|:---:|:---:|:---:|:---:|:---:|:---:|
| **容器沙箱** | ✅ Docker | ✅ 6種 | ✅ WASM | ✅ 容器預設 | ✅ WASM | ✅ Landlock |
| **seccomp/AppArmor** | ✅ | ❌ | ❌ | ✅ 預設 | ❌ | ❌ |
| **加密** | SecretRef | 憑證池 | ✅ AES-256 | ❌ | ❌ | ❌ |
| **DM Policy** | ✅ 完整 | ✅ 完整 | ❌ | ❌ | ❌ | ❌ |
| **Tool Policy** | ✅ 完整 | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Tool Guard** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Security Audit** | ✅ 全套 | ❌ | ❌ | ❌ | ✅ 可審計 | ❌ |
| **Prompt 防禦** | ⚠️ 基礎 | ❌ | ✅ 專項 | ❌ | ❌ | ❌ |
| **Zod Validation** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **記憶體安全語言** | ❌ TS | ❌ Py | ✅ Rust | ❌ TS | ✅ Rust | ✅ Rust |
| **最小攻擊面** | ❌ 大 | ❌ | ⚠️ | ❌ | ✅ 單一 | ✅ 8.8MB |

### 安全模式總結

| 安全模式 | 代表專案 | 適用場景 |
|----------|----------|----------|
| **🔐 深度防禦** | IronClaw、Moltis | 金融、醫療、區塊鏈 |
| **🛡️ 層層設卡** | OpenClaw、Hermes | 企業、生產環境 |
| **📦 容器隔離** | NanoClaw | 企業 K8s 部署 |
| **💡 輕量信任** | ZeroClaw、NullClaw | IoT、邊緣、CI/CD |
| **👁️ 隱私優先** | CoPaw、NanoBot | 個人使用、本地優先 |

---

## 4. 📡 通訊平台覆蓋

### 所有變體通訊平台合併統計

```
通訊平台覆蓋全景 (合併 14 變體 + Hermes)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╸

 平台                  │ 支援的專案數量（滿分15）
───────────────────────┤
 WhatsApp              │ █████████████████████  OpenClaw/NanoBot/ZeroClaw/NanoClaw/TinyAGI/Hermes...
 Telegram              │ █████████████████████  OpenClaw/NanoBot/ZeroClaw/NanoClaw/IronClaw/TinyAGI/Hermes
 Discord               │ ███████████████████░░  OpenClaw/NanoBot/ZeroClaw/NanoClaw/TinyAGI/NullClaw/Hermes...
 Signal                │ ███████████████░░░░░░  OpenClaw/ZeroClaw/NullClaw/Hermes
 Slack                 │ ███████████████░░░░░░  OpenClaw/NanoBot/ZeroClaw/NanoClaw/IronClaw/NullClaw/Hermes
 iMessage/BlueBubbles  │ █████████░░░░░░░░░░░░  OpenClaw/ZeroClaw
 Matrix                │ ████████████░░░░░░░░░  OpenClaw/NanoBot/ZeroClaw/NullClaw/Hermes
 微信 (WeChat)         │ ████████░░░░░░░░░░░░░  OpenClaw(插件)/NanoBot(原生)/ZeroClaw/Mochat
 钉钉 (DingTalk)       │ ████████░░░░░░░░░░░░░  NanoBot/ZeroClaw/Hermes
 飞书 (Feishu)         │ ████████░░░░░░░░░░░░░  OpenClaw/NanoBot/ZeroClaw/Hermes
 LINE                  │ ██████░░░░░░░░░░░░░░░  OpenClaw/NanoBot
 QQ                    │ ██████░░░░░░░░░░░░░░░  NanoBot/ZeroClaw
 企业微信 (WeCom)      │ ██████░░░░░░░░░░░░░░░  NanoBot/ZeroClaw/Hermes
 Gmail/Email          │ ████████░░░░░░░░░░░░░  NanoBot/ZeroClaw/NanoClaw/Hermes
 Mattermost            │ ██████████░░░░░░░░░░░  OpenClaw/ZeroClaw/Hermes
 Google Chat           │ ███████░░░░░░░░░░░░░░  OpenClaw
 IRC                   │ ███████░░░░░░░░░░░░░░  OpenClaw/ZeroClaw
 MS Teams              │ ███████░░░░░░░░░░░░░░  OpenClaw
 Nostr                 │ ███████░░░░░░░░░░░░░░  OpenClaw/ZeroClaw
 MQTT (IoT)            │ ███████░░░░░░░░░░░░░░  ZeroClaw/PicoClaw
 X (Twitter)           │ ██████░░░░░░░░░░░░░░░  ZeroClaw
 Twitch                │ ██████░░░░░░░░░░░░░░░  OpenClaw
 Bluesky               │ ██████░░░░░░░░░░░░░░░  ZeroClaw
 Reddit                │ ██████░░░░░░░░░░░░░░░  ZeroClaw
 LinkedIn              │ ██████░░░░░░░░░░░░░░░  ZeroClaw
 Nextcloud Talk        │ ███████░░░░░░░░░░░░░░  OpenClaw/ZeroClaw
 Zalo (越南通訊)       │ ██████░░░░░░░░░░░░░░░  OpenClaw
 WebSocket/Web       │ ███████████████████░░  OpenClaw/IronClaw/ZeroClaw/PicoClaw/Moltis/Hermes
 CLI/REPL            │ █████████░░░░░░░░░░░░  OpenClaw/IronClaw/ZeroClaw
 macOS App           │ ██████░░░░░░░░░░░░░░░  OpenClaw/LobsterAI
 iOS App             │ ██████░░░░░░░░░░░░░░░  OpenClaw/LobsterAI
 Android App         │ ██████░░░░░░░░░░░░░░░  OpenClaw/LobsterAI
 SMS                   │ ████░░░░░░░░░░░░░░░░░  Hermes ★獨特
 Home Assistant        │ ████░░░░░░░░░░░░░░░░░  Hermes ★獨特
 API Server            │ ████░░░░░░░░░░░░░░░░░  Hermes ★獨特
 Open WebUI            │ ████░░░░░░░░░░░░░░░░░  Hermes ★獨特
 Webhook               │ ████░░░░░░░░░░░░░░░░░  Hermes
```

### 平台覆蓋統計表

| 專案 | 平台數 | 獨特平台 | 與其他人共享的 |
|------|--------|----------|---------------|
| **ZeroClaw** | **28** | MQTT, Bluesky, Reddit, LinkedIn, X, WATI, Mochat, Linq, Notion, ClawdTalk | 20 |
| **OpenClaw** | **25+** | Google Chat, Teams, Twitch, Zalo, WeChat(插件), macOS/iOS/Android | 15 |
| **Hermes Agent** | **~17** | **Email, SMS, DingTalk, WeCom, Home Assistant, API Server, Open WebUI** | ~10 |
| **NanoBot** | **12** | QQ, WeCom(原生), Mochat, Email | 9 |
| **NullClaw** | **19** | 推斷含 Discord/Telegram/Slack/WhatsApp/Signal/Matrix | 15 |
| **NanoClaw** | ~6 | Gmail(原生) | WhatsApp/TG/Slack/Discord |
| **IronClaw** | 4 | REPL | Telegram/Slack |
| **TinyAGI** | 3 | 無獨有 | Discord/WhatsApp/Telegram |

> **Hermes Agent 獨特貢獻**：唯一同時支援 SMS、Home Assistant（智能家居）、原生 API Server、Open WebUI 的專案，在企業通訊（DingTalk/WeCom）和自動化/IoT 領域有最廣覆蓋。

### 平台類型分布

```
                    通訊平台類型分布（跨所有變體）

 IM 即時通訊 ████████████████████████████████████  ~15 個
  社群平台  ████████████████████                  ~7 個 (X/Nostr/Bluesky/Reddit/LinkedIn/Twitch)
  企業通訊  ████████████████████                  ~9 個 (Teams/Slack/Mattermost/Nextcloud/DingTalk/WeCom)
 中國平台  ████████████████                        ~6 個 (微信/飛書/釘釘/QQ/企業微信)
 IoT 協議  ████████                                MQTT/Serial/GPIO/Home Assistant
 原生 App  ████████                                  macOS/iOS/Android
 瀏覽器    ████████████                              WebSocket/WebChat/REPL/CLI/Open WebUI
 簡訊/Email ████████                                SMS/Email（Hermes 獨家）
 API/Webhook ████████                               API Server/Webhook（Hermes 獨家）
```

---

## 5. 🧠 記憶與學習

### 記憶體系統比較矩陣

| 專案 | 記憶類型 | 向量搜尋 | FTS | 上下文壓縮 | 隔離記憶 | Skill 系統 | 外部 Memory |
|------|:--------:|:--------:|:---:|:---------:|:--------:|:---------:|:----------:|
| **OpenClaw** | MemoryIndexManager | ✅ | ✅ | ✅ | ✅ per-agentDir | ✅ SKILL.md + ClawHub | ✅ Plugin |
| **Hermes** | SQLite + Markdown | ❌ | ✅ FTS5 | ✅ | ✅ via Profiles | ✅ SKILL.md + Hub | ✅ Honcho/Mem0 |
| **NanoBot** | Token-based | ❌ | ❌ | ✅ Token 截斷 | 基礎 | ❌ | ❌ |
| **NanoClaw** | 內建 Memory | ❌ | ❌ | ❌ | ✅ per-容器 | ❌ | ❌ |
| **ZeroClaw** | SOPs + 檔案 | ❌ | ❌ | ❌ | 基礎 | ❌ | ❌ |
| **PicoClaw** | 輕量檔案 | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **CoPaw** | 用戶控制記憶 | ⚠️ | ❌ | ❌ | ✅ | ❌ | ❌ |
| **IronClaw** | PostgreSQL + pgvector | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| **Moltis** | 持久化記憶 | ⚠️ | ⚠️ | ⚠️ | ❌ | ❌ | ❌ |
| **TinyAGI** | 工作區記憶 | ❌ | ❌ | ❌ | ✅ per-agent | ❌ | ❌ |
| **LobsterAI** | Cowork 上下文 | ❌ | ❌ | ⚠️ | ❌ | ❌ | ❌ |
| **NullClaw** | 極簡檔案 | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **ZeptoClaw** | longterm_memory | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **RivonClaw** | 繼承 OpenClaw + 反饋演化 | ✅ | ✅ | ✅ | ✅ | ✅ 繼承 | ✅ 繼承 |

### 記憶能力排名

```
記憶能力等級 (由強到弱)

T0 - 超群   ████████████████████████  OpenClaw + Hermes + RivonClaw
             向量搜尋 + FTS + 壓縮 + Skill Hub + 外部 integration

T1 - 先進   ████████████████████░░░░  IronClaw
             PostgreSQL + pgvector + FTS + ACID

T2 - 良好   ████████████████░░░░░░░░  NanoClaw + TinyAGI
             有隔離記憶但無高級搜尋

T3 - 基礎   ████████████░░░░░░░░░░░░  NanoBot + CoPaw + Moltis + LobsterAI + ZeptoClaw
             基本記憶持久化

T4 - 極簡   ████████░░░░░░░░░░░░░░░░  NullClaw + ZeroClaw + PicoClaw
             檔案級記憶最小化
```

### 記憶模式分類

| 記憶模式 | 代表專案 | 特色 |
|----------|----------|------|
| **🧩 索引型記憶** | OpenClaw, Hermes | Markdown 檔案索引 + FTS + 上下文壓縮 |
| **🗄️ 資料庫記憶** | IronClaw | PostgreSQL ACID + pgvector 向量搜尋 |
| **📦 容器隔離記憶** | NanoClaw | 每容器獨立，K8s volume 持久化 |
| **⏱️ Token 計數** | NanoBot | 以 Token 為單位控制歷史長度 |
| **🌱 進化記憶** | RivonClaw | Agent 自行演化，反饋學習 |
| **📁 檔案記憶** | ZeroClaw, NullClaw | 輕量檔案儲存，適用於資源受限 |
| **🏢 團隊記憶** | TinyAGI | Agent/Team 級隔離工作區 |

---

## 6. 🤖 多 Agent 能力

### 子代理/委派能力比較

```
多 Agent 能力評分 (滿分 10)

OpenClaw    ████████████████████████████████████████  10/10  ★★★★★
  ACP + ACPX + sessions_spawn/yield + agentToAgent
  + Cross-agent QMD + Sub-agent Registry + Delivery Dispatch
  + Multi-agent routing + 外部 CLI 委派 (5種)

TinyAGI    ██████████████████████████████░░░░░░░░░░  7.5/10  ★★★★☆
  Multi-agent + Multi-teams + Multi-channel
  Isolated Workspaces + Orchestrator

NanoClaw   ████████████████████████████░░░░░░░░░░░░  7/10  ★★★★☆
  容器級隔離 + Gateway 路由 + K8s 擴展

NanoBot    ████████████████████████░░░░░░░░░░░░░░░░  6/10  ★★★☆☆
  Subagent 系統 + 平行執行 + 結果匯總

Hermes     █████████████████████████████▒░░░░░░░░░░  7/10  ★★★★☆
  delegate_task (3 max) + isolated context
  + 6 種執行後端 + 完整 CLI 多實例

ZeroClaw   ██████████████████████░░░░░░░░░░░░░░░░░░  5/10  ★★★☆☆
  Hands (同程序多 Agent) + SOPs 事件驅動

CoPaw      █████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  2/10  ★☆☆☆☆
  個人助理定位，無原生多 Agent

Moltis     █████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  2/10  ★☆☆☆☆
  單一 Agent 伺服器，WASM 隔離非多 Agent

LobsterAI  █████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  2/10  ★☆☆☆☆
  單一強力 Agent，Cowork 模式

RivonClaw  █████████████████████████████▒░░░░░░░░░░  7/10  ★★★★☆
  繼承 OpenClaw 完整 ACP 能力

NullClaw   █████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  2/10  ★☆☆☆☆
  單 Agent 設計，天生免疫遞迴

ZeptoClaw  █████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  1/10  ★☆☆☆☆
  實驗中，單一 Agent
```

### 外部 CLI 委派能力（ACPX 生態）

| 專案 | 外部 CLI 委派 | 支援的 CLI | 通訊方式 |
|------|:-------------:|-----------|----------|
| **OpenClaw** | ✅ ACPX Harness | Claude Code, Codex, Gemini, Kiro, Cursor | stdin/stdout JSON-RPC |
| **RivonClaw** | ✅ 繼承 | 同 OpenClaw | 繼承 |
| 其他所有 | ❌ | - | - |

### 多 Agent 架構模式

| 模式 | 代表 | 描述 |
|------|------|------|
| **🔗 協議委派** | OpenClaw | ACP 協議 + ACPX 外部 CLI，跨程序通訊 |
| **📦 容器隔離** | NanoClaw | 每 Agent = 獨立容器，透過 Gateway 路由 |
| **🏢 團隊編排** | TinyAGI | Multi-team Orchestrator，角色化 Agent |
| **⚡ 同程序協調** | ZeroClaw | Hands（Rust native），同程序低延遲 |
| **🛢️ 平行委派** | NanoBot | Python asyncio 平行 subagent |

---

## 7. 💾 效能與資源

### 記憶體/啟動時間/二進制大小

```
二進制大小對比（由小到大）

ZeptoClaw  ███                                    ~2MB (估算)
NullClaw   ████                                   ~678 KB  ← 最小！
PicoClaw   ████████                               ~10-20MB (Go 編譯)
ZeroClaw   ███████████                            ~8.8MB (Rust 靜態)
Moltis     ██████████                             ~15-30MB (Rust)
IronClaw   ███████████                            ~20MB (Rust + deps)
CoPaw      ██████████████████████████████         pip 安裝，~50-100MB
NanoBot    ███████████████████████████████        pip 安裝，~100-200MB
TinyAGI    ████████████████████████████████████   npm + node_modules
RivonClaw  ████████████████████████████████████   npm + OpenClaw 依賴
LobsterAI  ██████████████████████████████████     跨平台原生 App
NanoClaw   ████████████████████████████████████   Docker Image
OpenClaw   ██████████████████████████████████████  npm + node_modules(數百MB)  ← 最大
```

```
啟動時間對比

NullClaw   ░░                                     <2ms  ← 最快！
ZeroClaw   ██                                     <10ms
ZeptoClaw  █████                                  <50ms (估算)
PicoClaw   ███████                                ~100ms (Go)
IronClaw   █████████                              ~200ms
Moltis     ███████████                            ~300ms
Hermes     ██████████████████████                 ~2-5s
NanoBot    ██████████████████████                 ~2-5s
OpenClaw   ████████████████████████████████       500ms - 3s
TinyAGI    ████████████████████████████████████    3-10s
LobsterAI  █████████████████████████████████████  5-15s
NanoClaw   ██████████████████████████████████████  10-30s (容器啟動)
```

```
RAM 佔用（常駐）

NullClaw   ██                                     ~1MB RSS  ← 最低！
ZeroClaw   ████                                   ~5MB RAM
ZeptoClaw  █████                                  <20MB
PicoClaw   █████                                  <10MB
Moltis     ████████                               ~30-50MB
IronClaw   ██████████                             ~50-100MB (含 PG)
NanoBot    ████████████████                       ~100-200MB
OpenClaw   ████████████████████████               ~100-300MB
Hermes     ██████████████████████████             ~200-400MB
TinyAGI    ██████████████████████████████         ~300-500MB
NanoClaw   ██████████████████████████████████████ ~1GB+ (容器組)
LobsterAI  ██████████████████████████████████████ ~500MB+ (原生 App)
```

### 效能總結表

| 指標 | 冠軍 | 數值 | 亞軍 | 數值 |
|------|------|------|------|------|
| 🏆 **最小二進制** | NullClaw | ~678 KB | ZeptoClaw | ~2MB |
| 🏆 **最快啟動** | NullClaw | <2ms | ZeroClaw | <10ms |
| 🏆 **最低 RAM** | NullClaw | ~1MB RSS | ZeroClaw | <5MB |
| 🏆 **最多平台** | ZeroClaw | 28 個 | OpenClaw | 25+ |
| 🏆 **最少依賴** | NullClaw | 0 | ZeroClaw | 0 |
| 🏆 **最強安全** | IronClaw | 92/100 | Moltis | 85/100 |
| 🏆 **最佳記憶** | OpenClaw | 超群 | IronClaw | 先進 |
| 🏆 **最完整** | OpenClaw | 全能 | Hermes | 全面 |

---

## 8. 🎯 使用場景推薦矩陣

### 場景 × 專案推薦

| 使用場景 | 首選 | 次選 | 理由 |
|----------|------|------|------|
| **🚀 快速原型** | NanoBot | CoPaw | `pip install`、最低門檻 |
| **🔒 金融/醫療級安全** | IronClaw | Moltis | WASM + 加密 + 可審計 |
| **🌐 最大通訊覆蓋** | ZeroClaw | OpenClaw | 28 個 / 25+ 平台 |
| **🤖 多 Agent 協作** | OpenClaw | TinyAGI | ACP + ACPX 完整生態 |
| **🏭 IoT / 嵌入式** | PicoClaw | ZeroClaw | $10 硬體 / 5MB RAM |
| **⚡ CI/CD 自動化** | NullClaw | ZeroClaw | <2ms 啟動、零依賴 |
| **🏢 企業部署** | NanoClaw | CoPaw | K8s-ready / 阿里雲 |
| **🇨🇳 中國市場** | NanoBot | LobsterAI | 微信/飛書/釘釘 / 網易 |
| **👨‍💻 開發自動化** | OpenClaw | NanoClaw | ACPX 委派 5 種 CLI |
| **🎨 全場景桌面** | LobsterAI | RivonClaw | macOS/Win/Linux/Mobile |
| **📱 原生移動 App** | OpenClaw | LobsterAI | macOS/iOS/Android Node |
| **🗣️ 語音互動** | OpenClaw | Moltis | Voice Wake + Talk Mode |
| **🤯 個人公司** | TinyAGI | OpenClaw | Multi-team 編排 |
| **📦 單一 Binary 部署** | Moltis | NullClaw | Rust + 零依賴 |
| **🧪 學習/探索** | ZeptoClaw | NanoBot | 輕量易讀 / 實驗性質 |
| **🪆 Easy Mode** | RivonClaw | — | 零配置自然語言規則 |
| **🔍 個人隱私** | CoPaw | NanoBot | 記憶完全本地控制 |
| **🌊 極限資源受限** | NullClaw | ZeroClaw | 678KB / 5MB RAM / <10ms |
| **🔗 Provider 无关/L多模型** | **Hermes** | OpenClaw | 18+ providers, Smart Model Routing, Credential Pool 輪轉 |
| **🏠 智能家居整合** | **Hermes** | PicoClaw | Home Assistant 原生整合 + IoT Gateway |
| **📧 企業通訊整合** | **Hermes** | NanoBot | Email/SMS/DingTalk/WeCom 全覆蓋 |
| **🧠 自我學習/上下文** | **Hermes** | OpenClaw | MEMORY.md/USER.md + Context Compression + Skills Hub |
| **🔐 生產級 Agent 安全** | IronClaw | Hermes | Defense-in-Depth + Tirith + SSRF + 審批機制 |
| **💰 成本控制/省錢模式** | **Hermes** | — | Auxiliary Client 模式、Credential Pool 輪轉 |

### 使用者類型推薦

```
開發者 ──→ 想要完全控制
  ├─ OpenClaw (全能)
  ├─ NanoClaw (容器化)
  └─ ZeroClaw (極致效能)

非技術用戶 ──→ 開箱即用
  ├─ RivonClaw (Easy Mode)
  ├─ LobsterAI (全平台桌面)
  └─ CoPaw (簡單 Python)

企業 IT ──→ 安全 + 擴展
  ├─ IronClaw (最高安全)
  ├─ NanoClaw (K8s 部署)
  └─ Hermes (Provider-agnostic + 多平台 gateway + 審批)

IoT 工程師 ──→ 低資源
  ├─ PicoClaw (IoT 原生)
  ├─ ZeroClaw (硬體集成)
  └─ Hermes (Home Assistant 整合)

研究人員 ──→ 可定制
  ├─ Hermes (Provider-agnostic, 18+ LLM)
  ├─ NanoBot (代碼量少 99%)
  └─ Moltis (可審計)

創業者/個人公司 ──→ 多角色協作
  ├─ TinyAGI (一人公司)
  ├─ OpenClaw (全能)
  └─ Hermes (delegate_task + 多平台通訊)
```

---

## 9. 📝 結論與建議

### 關鍵發現

```
┌──────────────────────────────────────────────────────────┐
│                    🔑 6 大關鍵發現                         │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  ① OpenClaw 是絕對核心                                   │
│     347K stars, 佔生態系總 stars ~68%                      │
│     是唯一真正全功能的個人 AI Assistant 框架                 │
│                                                          │
│  ② 語言分佈極度多樣化                                     │
│     TypeScript(5) Rust(5) Python(3) Go(1) Zig(1)          │
│     反映社群對「AI Agent 應該用什麼語言寫」的多元思考         │
│                                                          │
│  ③ 安全是最大分歧點                                       │
│     從 IronClaw 92 分到 ZeptoClaw 45 分                   │
│     安全投入與功能複雜度之間存在明確 trade-off               │
│                                                          │
│  ④ RivonClaw 揭示了重要市場需求                            │
│     「太難配置」是 OpenClaw 的最大痛點                      │
│     Easy Mode + 自然語言規則有巨大市場空間                   │
│                                                          │
│  ⑤ IoT + AI Agent 是增長最快領域                           │
│     ZeroClaw(28平台) + PicoClaw(Sipeed) + Hermes(HA)    │
│     從桌面擴展到嵌入式是下一波浪潮                           │
│                                                          │
│  ⑥ Hermes Agent 代表了 Provider-agnostic 趨勢              │
│     18+ providers + 通用型架構 + 17 平台通訊               │
│     多模型路由 + 省錢模式是獨一無二的差異化                   │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### 建議

#### 對不同用戶的建議

| 用戶類型 | 建議 | 理由 |
|----------|------|------|
| **新用戶** | 從 RivonClaw 或 NanoBot 入手 | 最低入門門檻 |
| **進階用戶** | 直接使用 OpenClaw | 功能最完整、社群最活躍 |
| **安全敏感** | IronClaw 或 Moltis | WASM + 審計 + 加密 |
| **IoT 開發者** | PicoClaw + ZeroClaw | 硬體集成最完善 |
| **Python 開發者** | NanoBot 或 CoPaw | Python 生態、pip install |
| **Rust 開發者** | ZeroClaw 或 IronClaw | Rust 實現、可閱讀原始碼 |
| **企業 IT** | NanoClaw (K8s) + OpenClaw (通訊) | 最佳組合 |
| **多模型使用者** | **Hermes Agent** | 18+ providers、Smart Model Routing、Auxiliary 省錢 |
| **中國企業用戶** | Hermes Agent | DingTalk + WeCom + Feishu 原生支援 |
| **智能家居愛好者** | **Hermes Agent** | Home Assistant 原生整合 |

#### 對 OpenClaw 核心開發者的建議

1. **📱 強化 Easy Mode**：RivonClaw 的成功證明市場需要零配置入口
2. **🔒 安全白皮書**：統一的安全框架可減少各安全變體的碎片化
3. **🤝 跨專案標準化**：SKILL.md、MemoryIndex 格式可作為生態系標準
4. **📡 平台 Plugin SDK**：讓 NanoBot/Hermes 等變體的獨家平台能被 OpenClaw 重用
5. **🔧 CLI 工具鏈**：統一各變體的安裝/配置/遷移體驗
6. **🔄 多模型路由**：參考 Hermes 的 Provider-agnostic 設計，支援更靈活的 LLM 切換

---

## 10. 🗺️ 生態系演進預測

### 短期預測（2026 Q2-Q3）

```
2026 Q2-Q3 預測
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📈 成長中
 ├── RivonClaw → 可能成為 OpenClaw 官方 Easy Mode
 ├── ZeptoClaw → 從實驗走向 beta，完善工具集
 ├── Hermes → 持續擴展通訊平台，Skills Hub 成長
 └── PicoClaw → Sipeed 硬體整合加速，邊緣 AI Agent 普及

🔮 潛在合併
 ├── IronClaw + Moltis → 統一 WASM 安全框架
 ├── NanoBot + CoPaw → 統一 Python AI Agent 基礎庫
 └── OpenClaw Plugin 吸收 Hermes 獨特平台(Email/SMS/HA)

⚡ 新趨勢
 ├── "Claw-as-a-Service" 雲端託管版
 ├── Agent Marketplace / Plugin 商店
 ├── 跨框架通訊協議（類似 Matrix for Agents）
 └── Provider-agnostic 架構成為主流（Hermes 引領）
```

### 中期預測（2026 Q4 - 2027 Q2）

```
2026 Q4 - 2027 Q2 預測
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🏢 企業化
 ├── OpenClaw Enterprise → 企業級付費版（SSO、審計、SLA）
 ├── 合規框架（GDPR、HIPAA、SOC2）
 └── 與 Kubernetes/雲平台深度整合

🧠 AI 原生
 ├── 本地 LLM 支援（Ollama、llama.cpp）
 ├── 多模態 Agent（Vision、Audio、Control）
 └── 自主 Agent（自我學習、自我優化）← Hermes 已開始

🌐 互操作
 ├── 跨框架 Agent 通訊協議
 ├── 統一 Skill 格式（SKILL.md → 業界標准）
 ├── Agent 身份認證（DID/Web5 風格）
 └── Provider Hub（參考 Hermes 的 18+ providers 架構）
```

### 長期預測（2027+）

```
2027+ 生態系願景

┌─────────────────────────────────────────────────────────┐
│                                                         │
│   Personal          Enterprise          IoT / Edge      │
│   Assistant         Platform            Autonomous      │
│   ┌───────────┐    ┌───────────┐       ┌───────────┐    │
│   │ OpenClaw  │    │ NanoClaw  │       │ ZeroClaw  │    │
│   │ +RivonClaw│←── │ Multi-    │      ↕│ PicoClaw  │    │
│   │ (Easy UI) │    │ agent K8s │       │ NullClaw  │    │
│   └─────┬─────┘    └───────────┘       └─────┬─────┘    │
│         │                                     │         │
│   ┌─────┴─────────────────────────────────────┴──────┐  │
│   │  ★ Hermes Agent (通用型 / Provider-agnostic)    │  │
│   │    跨域通訊: Email/SMS/HA/API + 多模型路由      │  │
│   └──────────────────────────────────────────────────┘  │
│                                                         │
│   ┌─────────────────────────────────────────────────┐   │
│   │           🦞 Cross-Claw Protocol                 │   │
│   │   (Agent-to-Agent Communication Standard)        │   │
│   └─────────────────────────────────────────────────┘   │
│                                                         │
│   統一 Skill Hub ←→ 統一 Memory Protocol ←→ 統一 Auth   │
│                                                         │
└─────────────────────────────────────────────────────────┘

預測結果：
 • OpenClaw 成為個人 AI Agent 的「Linux」— 核心 + 生態
 • 安全變體（IronClaw/Moltis）合併為統一的 Security Layer
 • IoT 變體（ZeroClaw/PicoClaw/NullClaw）形成 Edge Agent 聯盟
 • Hermes Agent 成為 Provider-agnostic 標準，跨框架多模型路由
 • 3 年內 Agent-to-Agent 通訊成為標配，類似今天的 REST API
```

### 風險與機遇

| 類型 | 描述 | 影響程度 |
|------|------|---------|
| ⚠️ **碎片化風險** | 14 個專案（+Hermes）導致社群分裂 | 高 |
| ⚠️ **維護者流失** | 多個小專案（<1K stars）可能停更 | 中 |
| ⚠️ **API 不兼容** | RivonClaw 等 wrapper 綁定 OpenClaw API | 中 |
| ✅ **標準化機遇** | SKILL.md、Memory 格式可成業界標準 | 高 |
| ✅ **IoT 藍海** | Agent + IoT 是未被填滿的藍海市場 | 高 |
| ✅ **企業需求** | NanoClaw/K8s 方向有巨大商業空間 | 高 |
| ✅ **Provider-agnostic** | Hermes 模式可推動跨模型標準化 | 高 |

---

## 📋 附錄

### A. 13 報告來源清單

| # | 報告檔名 | 比較對象 |
|---|----------|----------|
| 1 | `hermes-vs-openclaw-comparison.md` | Hermes Agent vs OpenClaw |
| 2 | `nanobot-vs-openclaw.md` | NanoBot vs OpenClaw |
| 3 | `zeroclaw-vs-openclaw.md` | ZeroClaw vs OpenClaw |
| 4 | `picoclaw-vs-openclaw.md` | PicoClaw vs OpenClaw |
| 5 | `nanoclaw-vs-openclaw.md` | NanoClaw vs OpenClaw |
| 6 | `copaw-vs-openclaw.md` | CoPaw vs OpenClaw |
| 7 | `ironclaw-vs-openclaw.md` | IronClaw vs OpenClaw |
| 8 | `lobsterai-vs-openclaw.md` | LobsterAI vs OpenClaw |
| 9 | `tinyagi-vs-openclaw.md` | TinyAGI vs OpenClaw |
| 10 | `nullclaw-vs-openclaw.md` | NullClaw vs OpenClaw |
| 11 | `moltis-vs-openclaw.md` | Moltis vs OpenClaw |
| 12 | `zeptoclaw-vs-openclaw.md` | ZeptoClaw vs OpenClaw |
| 13 | `rivonclaw-vs-openclaw.md` | RivonClaw vs OpenClaw |

### B. 快速選擇決策圖

```
你需要 AI Agent 嗎？ ── 是 ──→ 你有什麼需求？
                               │
                    ┌──────────┼──────────┐
                    │          │          │
                快速上手    最大功能    特殊需求
                    │          │          │
          NanoBot / RivonClaw   │    ┌────┼────┐
          CoPaw                OpenClaw  │    │    │
                                   │  安全  IoT  中國
                                   │  │    │    │
                               IronClaw Pico  NanoBot
                               Moltis  Zero  CoPaw
                               Hermes  Null  Hermes
                               NanoClaw      LobsterAI

                              多模型/省錢  智能家居
                                   │          │
                                 Hermes    Hermes
                                  (18+     (HA 原生)
                                providers)

                              企業通訊
                                   │
                                 Hermes
                              (DingTalk
                              /WeCom)
```

---

> **本報告基於 13 份深度比較報告編製，所有資料截至 2026-04-04。**  
> **用途：作為 40 分鐘 OpenClaw 生態系教學報告的基礎文檔。**  
> **文件路徑：** `/Users/owen/.hermes/hermes-agent/openclaw-comparison/SUMMARY-report.md`
